---
title: ASP.NET Core Web sunucusu uygulamasını Kestrel
author: rick-anderson
description: ASP.NET Core platformlar arası Web sunucusu olan Kestrel hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
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
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 268a6e71d3bd290ed614e70d963d653924cdcc43
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98252740"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="d900e-103">ASP.NET Core Web sunucusu uygulamasını Kestrel</span><span class="sxs-lookup"><span data-stu-id="d900e-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="d900e-104">[Tom Dykstra](https://github.com/tdykstra), [Chris](https://github.com/Tratcher), ve [Stephen halter](https://twitter.com/halter73) tarafından</span><span class="sxs-lookup"><span data-stu-id="d900e-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d900e-105">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d900e-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d900e-106">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak dahil edilen ve etkinleştirilen Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-106">Kestrel is the web server that's included and enabled by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d900e-107">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="d900e-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d900e-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d900e-108">HTTPS</span></span>
* <span data-ttu-id="d900e-109">[Http/2](xref:fundamentals/servers/kestrel/http2) (MacOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="d900e-109">[HTTP/2](xref:fundamentals/servers/kestrel/http2) (except on macOS&dagger;)</span></span>
* <span data-ttu-id="d900e-110">[WebSockets](xref:fundamentals/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="d900e-110">Opaque upgrade used to enable [WebSockets](xref:fundamentals/websockets)</span></span>
* <span data-ttu-id="d900e-111">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="d900e-111">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="d900e-112">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d900e-113">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d900e-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d900e-114">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d900e-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/5.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="get-started"></a><span data-ttu-id="d900e-115">başlarken</span><span class="sxs-lookup"><span data-stu-id="d900e-115">Get started</span></span>

<span data-ttu-id="d900e-116">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-116">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d900e-117">*Program.cs* içinde Yöntem şunu <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-117">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/5.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="d900e-118">Konak oluşturma hakkında daha fazla bilgi için, uygulamasının konak ve *Varsayılan Oluşturucu ayarlarını* *ayarlama* bölümüne bakın <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="d900e-118">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

## <a name="additional-resournces"></a><span data-ttu-id="d900e-119">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d900e-119">Additional resournces</span></span>

<a name="endpoint-configuration"></a>
* <xref:fundamentals/servers/kestrel/endpoints>
<a name="kestrel-options"></a>
* <xref:fundamentals/servers/kestrel/options>
<a name="http2-support"></a>
* <xref:fundamentals/servers/kestrel/http2>
<a name="when-to-use-kestrel-with-a-reverse-proxy"></a>
* <xref:fundamentals/servers/kestrel/when-to-use-a-reverse-proxy>
<a name="host-filtering"></a>
* <xref:fundamentals/servers/kestrel/host-filtering>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d900e-120">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="d900e-120">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
* <span data-ttu-id="d900e-121">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-121">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d900e-122">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-122">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>

> [!NOTE]
> <span data-ttu-id="d900e-123">ASP.NET Core 5,0 itibariyle Kestrel 'in libuv taşıması artık kullanılmıyor.</span><span class="sxs-lookup"><span data-stu-id="d900e-123">As of ASP.NET Core 5.0, Kestrel's libuv transport is obsolete.</span></span> <span data-ttu-id="d900e-124">Libuv taşıması, Windows ARM64 gibi yeni işletim sistemi platformlarını desteklemek için güncelleştirmeleri almaz ve sonraki bir sürümde kaldırılacaktır.</span><span class="sxs-lookup"><span data-stu-id="d900e-124">The libuv transport doesn't receive updates to support new OS platforms, such as Windows ARM64, and will be removed in a future release.</span></span> <span data-ttu-id="d900e-125">Kullanılmayan yönteme yapılan tüm çağrıları kaldırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> ve bunun yerine Kestrel 'in varsayılan yuva taşımasını kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-125">Remove any calls to the obsolete <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> method and use Kestrel's default Socket transport instead.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d900e-126">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d900e-126">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d900e-127">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-127">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d900e-128">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="d900e-128">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d900e-129">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d900e-129">HTTPS</span></span>
* <span data-ttu-id="d900e-130">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="d900e-130">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d900e-131">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="d900e-131">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d900e-132">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="d900e-132">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d900e-133">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-133">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d900e-134">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d900e-134">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d900e-135">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d900e-135">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/3.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d900e-136">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="d900e-136">HTTP/2 support</span></span>

<span data-ttu-id="d900e-137">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-137">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d900e-138">İşletim Sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="d900e-138">Operating system&dagger;</span></span>
  * <span data-ttu-id="d900e-139">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d900e-139">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d900e-140">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="d900e-140">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d900e-141">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d900e-141">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d900e-142">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="d900e-142">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d900e-143">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="d900e-143">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d900e-144">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-144">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d900e-145">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-145">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d900e-146">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-146">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d900e-147">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-147">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d900e-148">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="d900e-148">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d900e-149">.NET Core 3,0 ile başlayarak, HTTP/2 varsayılan olarak etkindir.</span><span class="sxs-lookup"><span data-stu-id="d900e-149">Starting with .NET Core 3.0, HTTP/2 is enabled by default.</span></span> <span data-ttu-id="d900e-150">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-150">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d900e-151">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="d900e-151">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d900e-152">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-152">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d900e-153">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="d900e-153">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d900e-154">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d900e-154">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d900e-156">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d900e-156">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d900e-158">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="d900e-158">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d900e-159">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="d900e-159">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d900e-160">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="d900e-160">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d900e-161">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-161">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d900e-162">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-162">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d900e-163">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="d900e-163">A reverse proxy:</span></span>

* <span data-ttu-id="d900e-164">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-164">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d900e-165">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d900e-165">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d900e-166">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-166">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d900e-167">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-167">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d900e-168">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-168">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d900e-169">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d900e-169">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d900e-170">ASP.NET Core uygulamalarda Kestrel</span><span class="sxs-lookup"><span data-stu-id="d900e-170">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d900e-171">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-171">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d900e-172">*Program.cs* içinde Yöntem şunu <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> çağırır <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-172">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="d900e-173">Konak oluşturma hakkında daha fazla bilgi için, uygulamasının konak ve *Varsayılan Oluşturucu ayarlarını* *ayarlama* bölümüne bakın <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="d900e-173">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="d900e-174">Çağrıldıktan sonra ek yapılandırma sağlamak için `ConfigureWebHostDefaults` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="d900e-174">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="d900e-175">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="d900e-175">Kestrel options</span></span>

<span data-ttu-id="d900e-176">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-176">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d900e-177">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="d900e-177">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d900e-178">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="d900e-178">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d900e-179">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="d900e-179">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d900e-180">Bu makalenin ilerleyen kısımlarında gösterilen örneklerde, Kestrel seçenekleri C# kodunda yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-180">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="d900e-181">Ayrıca, Kestrel seçenekleri bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-181">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d900e-182">Örneğin, [dosya yapılandırma sağlayıcısı](xref:fundamentals/configuration/index#file-configuration-provider) Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="d900e-182">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

> [!NOTE]
> <span data-ttu-id="d900e-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> ve [uç nokta yapılandırması](#endpoint-configuration) yapılandırma sağlayıcılarından yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-183"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="d900e-184">Kalan Kestrel yapılandırması C# kodunda yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-184">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="d900e-185">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="d900e-185">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d900e-186">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d900e-186">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d900e-187">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d900e-187">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d900e-188">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="d900e-188">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d900e-189">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-189">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="d900e-190">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="d900e-190">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d900e-191">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-191">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="d900e-192">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-192">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d900e-193">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="d900e-193">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d900e-194">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-194">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d900e-195">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="d900e-195">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d900e-196">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="d900e-196">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d900e-197">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-197">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d900e-198">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="d900e-198">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d900e-199">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="d900e-199">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d900e-200">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="d900e-200">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d900e-201">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-201">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d900e-202">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="d900e-202">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d900e-203">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="d900e-203">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d900e-204">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d900e-204">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d900e-205">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="d900e-205">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d900e-206">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d900e-206">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d900e-207">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-207">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d900e-208">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-208">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d900e-209">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="d900e-209">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d900e-210">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="d900e-210">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d900e-211">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-211">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d900e-212">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-212">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d900e-213">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-213">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d900e-214">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-214">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d900e-215">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-215">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d900e-216">*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d900e-216">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="d900e-217">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="d900e-217">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d900e-218"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Önceki örnekte başvurulan, `HttpContext.Features` http/2 isteklerinde hız sınırlarını değiştirmek, protokolün istek çoğullama DESTEĞI nedeniyle http/2 için genel olarak desteklenmediği IÇIN, http/2 istekleri için ' de mevcut değildir.</span><span class="sxs-lookup"><span data-stu-id="d900e-218">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d900e-219">Ancak, http <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` /2 istekleri için yine de vardır, çünkü okuma hızı sınırı,  `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` bir http/2 isteği için de olarak ayarlanarak tamamen istek temelli olarak devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-219">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="d900e-220">`IHttpMinRequestBodyDataRateFeature.MinDataRate`Bunun dışında bir değere ayarlamaya çalışılması veya BIR `null` `NotSupportedException` http/2 isteği verilmeye neden olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-220">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="d900e-221">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="d900e-221">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d900e-222">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="d900e-222">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d900e-223">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-223">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d900e-224">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="d900e-224">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d900e-225">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="d900e-225">Maximum streams per connection</span></span>

<span data-ttu-id="d900e-226">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-226">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d900e-227">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="d900e-227">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="d900e-228">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="d900e-228">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d900e-229">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-229">Header table size</span></span>

<span data-ttu-id="d900e-230">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="d900e-230">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d900e-231">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="d900e-231">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d900e-232">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="d900e-232">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="d900e-233">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d900e-233">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d900e-234">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-234">Maximum frame size</span></span>

<span data-ttu-id="d900e-235">`Http2.MaxFrameSize` sunucu tarafından alınan veya gönderilen HTTP/2 bağlantı çerçevesi yükünün izin verilen en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-235">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="d900e-236">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-236">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="d900e-237">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="d900e-237">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d900e-238">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-238">Maximum request header size</span></span>

<span data-ttu-id="d900e-239">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-239">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d900e-240">Bu sınır, sıkıştırılmış ve sıkıştırılmamış temsillerinde hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-240">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d900e-241">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-241">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="d900e-242">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d900e-242">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d900e-243">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-243">Initial connection window size</span></span>

<span data-ttu-id="d900e-244">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-244">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d900e-245">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="d900e-245">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d900e-246">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-246">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="d900e-247">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="d900e-247">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d900e-248">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-248">Initial stream window size</span></span>

<span data-ttu-id="d900e-249">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-249">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d900e-250">İstekleri ile de sınırlıdır `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="d900e-250">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="d900e-251">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-251">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="d900e-252">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="d900e-252">The default value is 96 KB (98,304).</span></span>

### <a name="trailers"></a><span data-ttu-id="d900e-253">Larına</span><span class="sxs-lookup"><span data-stu-id="d900e-253">Trailers</span></span>

[!INCLUDE[](~/includes/trailers.md)]

### <a name="reset"></a><span data-ttu-id="d900e-254">Sıfırla</span><span class="sxs-lookup"><span data-stu-id="d900e-254">Reset</span></span>

[!INCLUDE[](~/includes/reset.md)]

### <a name="synchronous-io"></a><span data-ttu-id="d900e-255">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="d900e-255">Synchronous I/O</span></span>

<span data-ttu-id="d900e-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="d900e-256"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d900e-257">`false` varsayılan değerdir.</span><span class="sxs-lookup"><span data-stu-id="d900e-257">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="d900e-258">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-258">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d900e-259">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d900e-259">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d900e-260">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="d900e-260">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d900e-261">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="d900e-261">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d900e-262">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-262">Endpoint configuration</span></span>

<span data-ttu-id="d900e-263">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="d900e-263">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d900e-264">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="d900e-264">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d900e-265">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="d900e-265">Specify URLs using the:</span></span>

* <span data-ttu-id="d900e-266">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="d900e-266">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d900e-267">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="d900e-267">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d900e-268">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="d900e-268">`urls` host configuration key.</span></span>
* <span data-ttu-id="d900e-269">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="d900e-269">`UseUrls` extension method.</span></span>

<span data-ttu-id="d900e-270">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d900e-270">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d900e-271">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="d900e-271">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d900e-272">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-272">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d900e-273">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="d900e-273">A development certificate is created:</span></span>

* <span data-ttu-id="d900e-274">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="d900e-274">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d900e-275">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-275">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d900e-276">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d900e-276">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d900e-277">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-277">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d900e-278"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="d900e-278">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d900e-279">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="d900e-279">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d900e-280">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="d900e-280">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d900e-281">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="d900e-281">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d900e-282">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-282">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d900e-283">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="d900e-283">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="d900e-284">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-284">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d900e-285">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="d900e-285">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d900e-286">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-286">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d900e-287">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="d900e-287">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

> [!NOTE]
> <span data-ttu-id="d900e-288">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-288">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="d900e-289">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="d900e-289">Configure(IConfiguration)</span></span>

<span data-ttu-id="d900e-290">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="d900e-290">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d900e-291">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-291">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d900e-292">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="d900e-292">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d900e-293">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-293">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d900e-294">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="d900e-294">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d900e-295">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-295">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d900e-296">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d900e-296">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="d900e-297">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="d900e-297">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d900e-298">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-298">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d900e-299">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-299">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d900e-300">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="d900e-300">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d900e-301">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="d900e-301">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d900e-302">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-302">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d900e-303">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-303">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d900e-304">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-304">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d900e-305">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-305">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d900e-306">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-306">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d900e-307">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d900e-307">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d900e-308">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-308">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d900e-309">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="d900e-309">Supported configurations described next:</span></span>

* <span data-ttu-id="d900e-310">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="d900e-310">No configuration</span></span>
* <span data-ttu-id="d900e-311">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="d900e-311">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d900e-312">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="d900e-312">Change the defaults in code</span></span>

<span data-ttu-id="d900e-313">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="d900e-313">*No configuration*</span></span>

<span data-ttu-id="d900e-314">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="d900e-314">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d900e-315">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="d900e-315">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d900e-316">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="d900e-316">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d900e-317">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-317">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d900e-318">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-318">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d900e-319">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="d900e-319">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d900e-320">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="d900e-320">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d900e-321">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan **httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="d900e-321">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },
      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },
      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },
      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="d900e-322">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-322">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d900e-323">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-323">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d900e-324">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="d900e-324">Schema notes:</span></span>

* <span data-ttu-id="d900e-325">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-325">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d900e-326">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-326">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d900e-327">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-327">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d900e-328">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-328">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d900e-329">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="d900e-329">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d900e-330">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-330">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d900e-331">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-331">The `Certificate` section is optional.</span></span> <span data-ttu-id="d900e-332">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-332">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d900e-333">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-333">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d900e-334">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="d900e-334">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d900e-335">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-335">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d900e-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="d900e-336">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="d900e-337">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="d900e-337">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d900e-338">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-338">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d900e-339">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-339">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d900e-340">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="d900e-340">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d900e-341">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="d900e-341">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d900e-342">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-342">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d900e-343">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-343">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d900e-344">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="d900e-344">*Change the defaults in code*</span></span>

<span data-ttu-id="d900e-345">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-345">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d900e-346">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-346">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });

    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.SslProtocols = SslProtocols.Tls12;
    });
});
```

<span data-ttu-id="d900e-347">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="d900e-347">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d900e-348">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-348">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d900e-349">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="d900e-349">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d900e-350">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-350">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d900e-351">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="d900e-351">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d900e-352">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-352">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d900e-353">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="d900e-353">SNI support requires:</span></span>

* <span data-ttu-id="d900e-354">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="d900e-354">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d900e-355">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="d900e-355">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d900e-356">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-356">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d900e-357">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-357">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d900e-358">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="d900e-358">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ListenAnyIP(5005, listenOptions =>
    {
        listenOptions.UseHttps(httpsOptions =>
        {
            var localhostCert = CertificateLoader.LoadFromStoreCert(
                "localhost", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var exampleCert = CertificateLoader.LoadFromStoreCert(
                "example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var subExampleCert = CertificateLoader.LoadFromStoreCert(
                "sub.example.com", "My", StoreLocation.CurrentUser,
                allowInvalid: true);
            var certs = new Dictionary<string, X509Certificate2>(
                StringComparer.OrdinalIgnoreCase);
            certs["localhost"] = localhostCert;
            certs["example.com"] = exampleCert;
            certs["sub.example.com"] = subExampleCert;

            httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
            {
                if (name != null && certs.TryGetValue(name, out var cert))
                {
                    return cert;
                }

                return exampleCert;
            };
        });
    });
});
```

### <a name="connection-logging"></a><span data-ttu-id="d900e-359">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="d900e-359">Connection logging</span></span>

<span data-ttu-id="d900e-360"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="d900e-360">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d900e-361">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-361">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d900e-362">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-362">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d900e-363">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-363">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d900e-364">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="d900e-364">Bind to a TCP socket</span></span>

<span data-ttu-id="d900e-365"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="d900e-365">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="d900e-366">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="d900e-366">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d900e-367">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-367">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d900e-368">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="d900e-368">Bind to a Unix socket</span></span>

<span data-ttu-id="d900e-369"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-369">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="d900e-370">NGINX yapılandırma dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="d900e-370">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d900e-371">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="d900e-371">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d900e-372">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="d900e-372">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="d900e-373">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="d900e-373">Port 0</span></span>

<span data-ttu-id="d900e-374">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-374">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d900e-375">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d900e-375">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d900e-376">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="d900e-376">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d900e-377">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="d900e-377">Limitations</span></span>

<span data-ttu-id="d900e-378">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="d900e-378">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d900e-379">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="d900e-379">`--urls` command-line argument</span></span>
* <span data-ttu-id="d900e-380">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="d900e-380">`urls` host configuration key</span></span>
* <span data-ttu-id="d900e-381">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="d900e-381">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d900e-382">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-382">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d900e-383">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="d900e-383">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d900e-384">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="d900e-384">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d900e-385">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d900e-385">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d900e-386">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-386">IIS endpoint configuration</span></span>

<span data-ttu-id="d900e-387">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="d900e-387">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d900e-388">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-388">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d900e-389">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="d900e-389">ListenOptions.Protocols</span></span>

<span data-ttu-id="d900e-390">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="d900e-390">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d900e-391">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="d900e-391">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d900e-392">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="d900e-392">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d900e-393">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="d900e-393">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d900e-394">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d900e-394">HTTP/1.1 only.</span></span> <span data-ttu-id="d900e-395">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-395">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d900e-396">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d900e-396">HTTP/2 only.</span></span> <span data-ttu-id="d900e-397">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-397">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d900e-398">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d900e-398">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d900e-399">HTTP/2, istemcinin TLS [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) el sıkışmasında http/2 seçmesini gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d900e-399">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d900e-400">`ListenOptions.Protocols`Herhangi bir uç nokta için varsayılan değer `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="d900e-400">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="d900e-401">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="d900e-401">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d900e-402">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d900e-402">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d900e-403">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="d900e-403">Renegotiation disabled</span></span>
* <span data-ttu-id="d900e-404">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="d900e-404">Compression disabled</span></span>
* <span data-ttu-id="d900e-405">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="d900e-405">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d900e-406">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="d900e-406">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="d900e-407">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="d900e-407">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="d900e-408">Şifre paketi yasaklandı.</span><span class="sxs-lookup"><span data-stu-id="d900e-408">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="d900e-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d900e-409">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d900e-410">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d900e-410">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d900e-411">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="d900e-411">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="d900e-412">Gerektiğinde belirli şifrelemeler için TLS el sıkışmaları için bağlantı temelinde filtre uygulamak üzere bağlantı ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-412">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="d900e-413">Aşağıdaki örnek, <xref:System.NotSupportedException> uygulamanın desteklemediği herhangi bir şifre algoritması için oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d900e-413">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="d900e-414">Alternatif olarak, [ılshandshakefeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) öğesini kabul edilebilir şifreleme paketleri listesi ile tanımlayın ve karşılaştırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-414">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="d900e-415">[CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) şifre algoritması ile hiçbir şifreleme kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-415">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="d900e-416">Bağlantı filtrelemesi, bir lambda aracılığıyla da yapılandırılabilir <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> :</span><span class="sxs-lookup"><span data-stu-id="d900e-416">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="d900e-417">Linux 'ta, <xref:System.Net.Security.CipherSuitesPolicy> TLS el sıkışmaları her bağlantı temelinde filtrelemek için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-417">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="d900e-418">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="d900e-418">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d900e-419">`CreateDefaultBuilder``serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="d900e-419">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d900e-420">Aşağıdaki *appsettings.json* örnek, tüm uç noktalar için varsayılan bağlantı protokolü olarak http/1.1 'yi kurar:</span><span class="sxs-lookup"><span data-stu-id="d900e-420">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="d900e-421">Aşağıdaki *appsettings.json* örnek, belirli bir uç nokta IÇIN http/1.1 bağlantı protokolünü belirler:</span><span class="sxs-lookup"><span data-stu-id="d900e-421">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="d900e-422">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="d900e-422">Protocols specified in code override values set by configuration.</span></span>

### <a name="url-prefixes"></a><span data-ttu-id="d900e-423">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="d900e-423">URL prefixes</span></span>

<span data-ttu-id="d900e-424">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-424">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d900e-425">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-425">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d900e-426">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="d900e-426">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d900e-427">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="d900e-427">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d900e-428">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="d900e-428">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d900e-429">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="d900e-429">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d900e-430">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="d900e-430">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d900e-431">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="d900e-431">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d900e-432">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="d900e-432">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d900e-433">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="d900e-433">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d900e-434">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-434">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d900e-435">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d900e-435">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d900e-436">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="d900e-436">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d900e-437">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-437">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d900e-438">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-438">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d900e-439">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d900e-439">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d900e-440">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="d900e-440">Host filtering</span></span>

<span data-ttu-id="d900e-441">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="d900e-441">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d900e-442">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="d900e-442">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d900e-443">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-443">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d900e-444">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="d900e-444">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d900e-445">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-445">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d900e-446">Ana bilgisayar filtreleme ara yazılımı, ASP.NET Core uygulamaları için örtük olarak sunulan [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-446">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="d900e-447">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-447">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d900e-448">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-448">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d900e-449">Ara yazılımı etkinleştirmek için `AllowedHosts` appSettings 'de bir anahtar tanımlayın *appsettings.json* / *. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="d900e-449">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d900e-450">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="d900e-450">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d900e-451">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d900e-451">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d900e-452">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-452">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d900e-453">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-453">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d900e-454">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="d900e-454">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d900e-455">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-455">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d900e-456">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="d900e-456">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="d900e-457">Libuv aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-457">Libuv transport configuration</span></span>

<span data-ttu-id="d900e-458">Libuv () kullanımını gerektiren projeler için <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> :</span><span class="sxs-lookup"><span data-stu-id="d900e-458">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A>):</span></span>

* <span data-ttu-id="d900e-459">[`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv)Uygulamanın proje dosyasına paket için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-459">Add a dependency for the [`Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv`](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d900e-460">Şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="d900e-460">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv%2A> on the `IWebHostBuilder`:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateHostBuilder(args).Build().Run();
      }

      public static IHostBuilder CreateHostBuilder(string[] args) =>
          Host.CreateDefaultBuilder(args)
              .ConfigureWebHostDefaults(webBuilder =>
              {
                  webBuilder.UseLibuv();
                  webBuilder.UseStartup<Startup>();
              });
  }
  ```

## <a name="http11-request-draining"></a><span data-ttu-id="d900e-461">HTTP/1.1 istek boşaltma</span><span class="sxs-lookup"><span data-stu-id="d900e-461">HTTP/1.1 request draining</span></span>

<span data-ttu-id="d900e-462">HTTP bağlantılarının açılması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-462">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="d900e-463">HTTPS için de kaynak kullanımı yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="d900e-463">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="d900e-464">Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-464">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="d900e-465">Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-465">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="d900e-466">Uygulama, `POST` sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü bir istek gibi her zaman istek gövdesini tüketmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-466">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="d900e-467">`POST`Yeniden yönlendirme durumunda:</span><span class="sxs-lookup"><span data-stu-id="d900e-467">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="d900e-468">İstemci, verilerin bir bölümünü zaten göndermiş olabilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="d900e-468">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="d900e-469">Sunucu 301 yanıtını yazar.</span><span class="sxs-lookup"><span data-stu-id="d900e-469">The server writes the 301 response.</span></span>
* <span data-ttu-id="d900e-470">`POST`Önceki istek gövdesinden gelen veriler tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-470">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="d900e-471">Kestrel, istek gövdesini boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-471">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="d900e-472">İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d900e-472">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="d900e-473">Boşaltma işlemi bağlantının yeniden kullanılmasına izin verme ve kalan verilerin boşaltıma süresi arasında bir ilerleme gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="d900e-473">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="d900e-474">Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-474">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="d900e-475">Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-475">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="d900e-476">Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d900e-476">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="d900e-477">Örneğin, istemciler sınırlı verilerin büyük bir düzeyi olabilir, bu nedenle karşıya yüklenen verileri sınırlamak bir öncelik olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-477">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="d900e-478">Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .</span><span class="sxs-lookup"><span data-stu-id="d900e-478">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="d900e-479">Çağırmanın uyarıları vardır `Abort` :</span><span class="sxs-lookup"><span data-stu-id="d900e-479">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="d900e-480">Yeni bağlantı oluşturma yavaş ve pahalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-480">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="d900e-481">İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-481">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="d900e-482">Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-482">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="d900e-483">Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-483">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="d900e-484">Örneğin, `Abort` kötü amaçlı istemciler veri almaya çalışıyorsa `POST` veya istemci kodunda büyük veya çok sayıda isteğe neden olan bir hata olduğunda çağırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-484">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="d900e-485">`Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.</span><span class="sxs-lookup"><span data-stu-id="d900e-485">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="d900e-486">Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-486">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="d900e-487">Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-487">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="d900e-488">Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="d900e-488">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="d900e-489">Beş saniyelik boşaltma zaman aşımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-489">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="d900e-490">Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir.</span><span class="sxs-lookup"><span data-stu-id="d900e-490">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="d900e-491">Ek istek gövdesi veri çerçeveleri yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-491">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="d900e-492">Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanabilmesi ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-492">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="d900e-493">Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.</span><span class="sxs-lookup"><span data-stu-id="d900e-493">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d900e-494">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d900e-494">Additional resources</span></span>

* <span data-ttu-id="d900e-495">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-495">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d900e-496">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-496">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d900e-497">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="d900e-497">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d900e-498">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d900e-498">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d900e-499">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-499">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d900e-500">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="d900e-500">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d900e-501">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d900e-501">HTTPS</span></span>
* <span data-ttu-id="d900e-502">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="d900e-502">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d900e-503">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="d900e-503">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d900e-504">HTTP/2 (macOS hariç &dagger; )</span><span class="sxs-lookup"><span data-stu-id="d900e-504">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d900e-505">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-505">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d900e-506">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d900e-506">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d900e-507">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d900e-507">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d900e-508">HTTP/2 desteği</span><span class="sxs-lookup"><span data-stu-id="d900e-508">HTTP/2 support</span></span>

<span data-ttu-id="d900e-509">Aşağıdaki temel gereksinimler karşılanıyorsa, [http/2](https://httpwg.org/specs/rfc7540.html) ASP.NET Core uygulamalar için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-509">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d900e-510">İşletim Sistemi&dagger;</span><span class="sxs-lookup"><span data-stu-id="d900e-510">Operating system&dagger;</span></span>
  * <span data-ttu-id="d900e-511">Windows Server 2016/Windows 10 veya üzeri&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d900e-511">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d900e-512">OpenSSL 1.0.2 veya üzerini içeren Linux (örneğin, Ubuntu 16,04 veya üzeri)</span><span class="sxs-lookup"><span data-stu-id="d900e-512">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d900e-513">Hedef Framework: .NET Core 2,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d900e-513">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d900e-514">[Uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı</span><span class="sxs-lookup"><span data-stu-id="d900e-514">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d900e-515">TLS 1,2 veya üzeri bağlantı</span><span class="sxs-lookup"><span data-stu-id="d900e-515">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d900e-516">&dagger;HTTP/2, gelecek sürümlerde macOS 'ta desteklenecektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-516">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d900e-517">&Dagger;Kestrel, Windows Server 2012 R2 ve Windows 8.1 'de HTTP/2 için sınırlı destek içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-517">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d900e-518">Bu işletim sistemlerinde kullanılabilir olan desteklenen TLS şifre paketlerinin listesi sınırlı olduğundan destek sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-518">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d900e-519">TLS bağlantılarının güvenliğini sağlamak için Eliptik Eğri dijital Imza algoritması (ECDSA) kullanılarak oluşturulan bir sertifika gerekli olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-519">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d900e-520">Bir HTTP/2 bağlantısı kurulduysa, [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) Reports `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="d900e-520">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d900e-521">HTTP/2 varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-521">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="d900e-522">Yapılandırma hakkında daha fazla bilgi için [Kestrel Options](#kestrel-options) ve [Listenoptions. Protocols](#listenoptionsprotocols) bölümlerine bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-522">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d900e-523">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="d900e-523">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d900e-524">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-524">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d900e-525">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="d900e-525">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d900e-526">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d900e-526">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d900e-528">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d900e-528">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d900e-530">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="d900e-530">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d900e-531">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="d900e-531">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d900e-532">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="d900e-532">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d900e-533">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-533">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d900e-534">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-534">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d900e-535">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="d900e-535">A reverse proxy:</span></span>

* <span data-ttu-id="d900e-536">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-536">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d900e-537">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d900e-537">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d900e-538">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-538">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d900e-539">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-539">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d900e-540">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-540">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d900e-541">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d900e-541">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d900e-542">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="d900e-542">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d900e-543">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="d900e-543">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d900e-544">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-544">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d900e-545">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-545">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="d900e-546">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="d900e-546">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="d900e-547">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu kullanın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="d900e-547">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d900e-548">Uygulama, `CreateDefaultBuilder` Konağı kurmak için çağırmazsa, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> çağrılmadan **önce** çağırın `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="d900e-548">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="d900e-549">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="d900e-549">Kestrel options</span></span>

<span data-ttu-id="d900e-550">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-550">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d900e-551">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="d900e-551">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d900e-552">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="d900e-552">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d900e-553">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="d900e-553">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d900e-554">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-554">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d900e-555">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="d900e-555">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="d900e-556">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="d900e-556">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d900e-557">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d900e-557">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d900e-558">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d900e-558">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d900e-559">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="d900e-559">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d900e-560">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-560">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="d900e-561">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="d900e-561">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d900e-562">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-562">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="d900e-563">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-563">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d900e-564">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="d900e-564">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d900e-565">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-565">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d900e-566">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="d900e-566">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d900e-567">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="d900e-567">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d900e-568">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-568">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d900e-569">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="d900e-569">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d900e-570">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="d900e-570">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d900e-571">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="d900e-571">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d900e-572">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-572">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d900e-573">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="d900e-573">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d900e-574">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="d900e-574">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d900e-575">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d900e-575">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d900e-576">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="d900e-576">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d900e-577">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d900e-577">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d900e-578">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-578">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d900e-579">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-579">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d900e-580">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="d900e-580">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d900e-581">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="d900e-581">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d900e-582">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-582">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d900e-583">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-583">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d900e-584">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-584">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d900e-585">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-585">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d900e-586">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-586">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d900e-587">*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d900e-587">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="d900e-588">Ara yazılım içindeki istek başına düşen minimum hız sınırlarını geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="d900e-588">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d900e-589">`HttpContext.Features`İstek çoğullama için protokol desteği nedeniyle, http/2 için bir istek başına hız sınırlarını değiştirmek, http/2 istekleri için, önceki örnekte başvurulan hiçbir oran özelliği mevcut değil.</span><span class="sxs-lookup"><span data-stu-id="d900e-589">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d900e-590">`KestrelServerOptions.Limits`Http/1. x ve http/2 bağlantılarına hala uygulanan sunucu genelindeki hız sınırları.</span><span class="sxs-lookup"><span data-stu-id="d900e-590">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d900e-591">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="d900e-591">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d900e-592">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-592">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d900e-593">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="d900e-593">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d900e-594">Bağlantı başına en fazla akış</span><span class="sxs-lookup"><span data-stu-id="d900e-594">Maximum streams per connection</span></span>

<span data-ttu-id="d900e-595">`Http2.MaxStreamsPerConnection` HTTP/2 bağlantısı başına eşzamanlı istek akışı sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-595">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d900e-596">Fazlalık akışlar reddedildi.</span><span class="sxs-lookup"><span data-stu-id="d900e-596">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="d900e-597">Varsayılan değer 100’dür.</span><span class="sxs-lookup"><span data-stu-id="d900e-597">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d900e-598">Üst bilgi tablosu boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-598">Header table size</span></span>

<span data-ttu-id="d900e-599">HPACK kod çözücüsü HTTP/2 bağlantıları için HTTP üstbilgilerini açar.</span><span class="sxs-lookup"><span data-stu-id="d900e-599">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d900e-600">`Http2.HeaderTableSize` HPACK kod çözücüsünün kullandığı üst bilgi sıkıştırma tablosunun boyutunu sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="d900e-600">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d900e-601">Değer sekizli cinsinden sağlanır ve sıfırdan büyük olmalıdır (0).</span><span class="sxs-lookup"><span data-stu-id="d900e-601">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="d900e-602">Varsayılan değer 4096 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d900e-602">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d900e-603">En büyük çerçeve boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-603">Maximum frame size</span></span>

<span data-ttu-id="d900e-604">`Http2.MaxFrameSize` alacak HTTP/2 bağlantı çerçevesi yükünün en büyük boyutunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-604">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="d900e-605">Değer sekizli cinsinden sağlanır ve 2 ^ 14 (16.384) ile 2 ^ 24-1 (16.777.215) arasında olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-605">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="d900e-606">Varsayılan değer 2 ^ 14 ' dir (16.384).</span><span class="sxs-lookup"><span data-stu-id="d900e-606">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d900e-607">En fazla istek üst bilgi boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-607">Maximum request header size</span></span>

<span data-ttu-id="d900e-608">`Http2.MaxRequestHeaderFieldSize` istek üst bilgisi değerlerinin sekizlisi cinsinden izin verilen en büyük boyutu belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-608">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d900e-609">Bu sınır, hem sıkıştırılmış hem de sıkıştırılmamış temsillerinde birlikte hem ad hem de değer için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-609">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d900e-610">Değer sıfırdan büyük (0) olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-610">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="d900e-611">Varsayılan değer 8.192 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d900e-611">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d900e-612">İlk bağlantı pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-612">Initial connection window size</span></span>

<span data-ttu-id="d900e-613">`Http2.InitialConnectionWindowSize` sunucu, bağlantı başına tüm istekler (akışlar) genelinde toplanan tek seferde sunucunun arabelleğe aldığı en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-613">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d900e-614">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="d900e-614">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d900e-615">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-615">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="d900e-616">Varsayılan değer 128 KB 'tır (131.072).</span><span class="sxs-lookup"><span data-stu-id="d900e-616">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d900e-617">İlk akış pencere boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-617">Initial stream window size</span></span>

<span data-ttu-id="d900e-618">`Http2.InitialStreamWindowSize` sunucu, istek başına bir kez (Stream) arabelleğe alınan en fazla istek gövde verilerini bayt cinsinden gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-618">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d900e-619">İstekleri ile de sınırlıdır `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="d900e-619">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d900e-620">Değer, 65.535 değerinden büyük veya buna eşit ve 2 ^ 31 (2.147.483.648) değerinden küçük olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-620">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="d900e-621">Varsayılan değer 96 KB 'tır (98.304).</span><span class="sxs-lookup"><span data-stu-id="d900e-621">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="d900e-622">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="d900e-622">Synchronous I/O</span></span>

<span data-ttu-id="d900e-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="d900e-623"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d900e-624">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="d900e-624">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d900e-625">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-625">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d900e-626">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d900e-626">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d900e-627">Aşağıdaki örnek, zaman uyumlu g/ç 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="d900e-627">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d900e-628">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="d900e-628">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d900e-629">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-629">Endpoint configuration</span></span>

<span data-ttu-id="d900e-630">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="d900e-630">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d900e-631">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="d900e-631">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d900e-632">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="d900e-632">Specify URLs using the:</span></span>

* <span data-ttu-id="d900e-633">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="d900e-633">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d900e-634">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="d900e-634">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d900e-635">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="d900e-635">`urls` host configuration key.</span></span>
* <span data-ttu-id="d900e-636">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="d900e-636">`UseUrls` extension method.</span></span>

<span data-ttu-id="d900e-637">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d900e-637">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d900e-638">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="d900e-638">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d900e-639">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-639">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d900e-640">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="d900e-640">A development certificate is created:</span></span>

* <span data-ttu-id="d900e-641">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="d900e-641">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d900e-642">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-642">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d900e-643">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d900e-643">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d900e-644">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-644">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d900e-645"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="d900e-645">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d900e-646">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="d900e-646">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d900e-647">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="d900e-647">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d900e-648">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="d900e-648">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d900e-649">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-649">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d900e-650">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="d900e-650">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d900e-651">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-651">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d900e-652">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="d900e-652">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d900e-653">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-653">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d900e-654">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="d900e-654">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d900e-655">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-655">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="d900e-656">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="d900e-656">Configure(IConfiguration)</span></span>

<span data-ttu-id="d900e-657">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="d900e-657">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d900e-658">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-658">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d900e-659">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="d900e-659">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d900e-660">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-660">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d900e-661">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="d900e-661">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d900e-662">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-662">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d900e-663">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d900e-663">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="d900e-664">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="d900e-664">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d900e-665">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-665">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d900e-666">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-666">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d900e-667">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="d900e-667">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d900e-668">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="d900e-668">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d900e-669">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-669">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d900e-670">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-670">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d900e-671">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-671">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d900e-672">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-672">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d900e-673">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-673">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d900e-674">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d900e-674">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d900e-675">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-675">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d900e-676">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="d900e-676">Supported configurations described next:</span></span>

* <span data-ttu-id="d900e-677">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="d900e-677">No configuration</span></span>
* <span data-ttu-id="d900e-678">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="d900e-678">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d900e-679">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="d900e-679">Change the defaults in code</span></span>

<span data-ttu-id="d900e-680">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="d900e-680">*No configuration*</span></span>

<span data-ttu-id="d900e-681">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="d900e-681">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d900e-682">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="d900e-682">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d900e-683">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="d900e-683">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d900e-684">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-684">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d900e-685">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-685">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d900e-686">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="d900e-686">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d900e-687">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="d900e-687">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d900e-688">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan **httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="d900e-688">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="d900e-689">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-689">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d900e-690">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-690">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d900e-691">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="d900e-691">Schema notes:</span></span>

* <span data-ttu-id="d900e-692">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-692">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d900e-693">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-693">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d900e-694">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-694">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d900e-695">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-695">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d900e-696">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="d900e-696">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d900e-697">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-697">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d900e-698">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-698">The `Certificate` section is optional.</span></span> <span data-ttu-id="d900e-699">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-699">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d900e-700">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-700">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d900e-701">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="d900e-701">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d900e-702">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-702">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d900e-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="d900e-703">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="d900e-704">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="d900e-704">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d900e-705">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-705">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d900e-706">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-706">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d900e-707">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="d900e-707">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d900e-708">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="d900e-708">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d900e-709">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-709">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d900e-710">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-710">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d900e-711">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="d900e-711">*Change the defaults in code*</span></span>

<span data-ttu-id="d900e-712">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-712">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d900e-713">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-713">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="d900e-714">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="d900e-714">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d900e-715">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-715">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d900e-716">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="d900e-716">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d900e-717">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-717">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d900e-718">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="d900e-718">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d900e-719">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-719">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d900e-720">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="d900e-720">SNI support requires:</span></span>

* <span data-ttu-id="d900e-721">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="d900e-721">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d900e-722">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="d900e-722">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d900e-723">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-723">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d900e-724">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-724">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d900e-725">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="d900e-725">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="connection-logging"></a><span data-ttu-id="d900e-726">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="d900e-726">Connection logging</span></span>

<span data-ttu-id="d900e-727"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="d900e-727">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d900e-728">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-728">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d900e-729">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-729">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d900e-730">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-730">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d900e-731">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="d900e-731">Bind to a TCP socket</span></span>

<span data-ttu-id="d900e-732"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="d900e-732">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="d900e-733">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="d900e-733">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d900e-734">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-734">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d900e-735">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="d900e-735">Bind to a Unix socket</span></span>

<span data-ttu-id="d900e-736"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-736">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="d900e-737">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="d900e-737">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d900e-738">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="d900e-738">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d900e-739">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="d900e-739">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="d900e-740">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="d900e-740">Port 0</span></span>

<span data-ttu-id="d900e-741">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-741">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d900e-742">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d900e-742">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d900e-743">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="d900e-743">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d900e-744">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="d900e-744">Limitations</span></span>

<span data-ttu-id="d900e-745">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="d900e-745">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d900e-746">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="d900e-746">`--urls` command-line argument</span></span>
* <span data-ttu-id="d900e-747">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="d900e-747">`urls` host configuration key</span></span>
* <span data-ttu-id="d900e-748">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="d900e-748">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d900e-749">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-749">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d900e-750">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="d900e-750">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d900e-751">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="d900e-751">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d900e-752">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d900e-752">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d900e-753">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-753">IIS endpoint configuration</span></span>

<span data-ttu-id="d900e-754">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="d900e-754">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d900e-755">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-755">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d900e-756">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="d900e-756">ListenOptions.Protocols</span></span>

<span data-ttu-id="d900e-757">`Protocols`Özelliği, `HttpProtocols` bir bağlantı uç noktasında veya sunucu IÇIN etkin HTTP protokollerini () belirler.</span><span class="sxs-lookup"><span data-stu-id="d900e-757">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d900e-758">`Protocols`Sabit listesinden özelliğe bir değer atayın `HttpProtocols` .</span><span class="sxs-lookup"><span data-stu-id="d900e-758">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d900e-759">`HttpProtocols` sabit listesi değeri</span><span class="sxs-lookup"><span data-stu-id="d900e-759">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d900e-760">Bağlantı protokolü izin verildi</span><span class="sxs-lookup"><span data-stu-id="d900e-760">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d900e-761">Yalnızca HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d900e-761">HTTP/1.1 only.</span></span> <span data-ttu-id="d900e-762">, TLS olmadan veya ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-762">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d900e-763">Yalnızca HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d900e-763">HTTP/2 only.</span></span> <span data-ttu-id="d900e-764">Yalnızca istemci [önceki bir bilgi modunu](https://tools.ietf.org/html/rfc7540#section-3.4)DESTEKLIYORSA, TLS olmadan kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-764">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d900e-765">HTTP/1.1 ve HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d900e-765">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d900e-766">HTTP/2 bir TLS ve [uygulama katmanı protokol anlaşması (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) bağlantısı gerektirir; Aksi takdirde, bağlantı varsayılan olarak HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d900e-766">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d900e-767">Varsayılan protokol HTTP/1.1 ' dir.</span><span class="sxs-lookup"><span data-stu-id="d900e-767">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="d900e-768">HTTP/2 için TLS kısıtlamaları:</span><span class="sxs-lookup"><span data-stu-id="d900e-768">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d900e-769">TLS sürüm 1,2 veya üzeri</span><span class="sxs-lookup"><span data-stu-id="d900e-769">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d900e-770">Yeniden anlaşma devre dışı</span><span class="sxs-lookup"><span data-stu-id="d900e-770">Renegotiation disabled</span></span>
* <span data-ttu-id="d900e-771">Sıkıştırma devre dışı</span><span class="sxs-lookup"><span data-stu-id="d900e-771">Compression disabled</span></span>
* <span data-ttu-id="d900e-772">En az kısa ömürlü anahtar değişim boyutları:</span><span class="sxs-lookup"><span data-stu-id="d900e-772">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d900e-773">Eliptik Eğri Diffie-Hellman (ECDHE) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bit minimum</span><span class="sxs-lookup"><span data-stu-id="d900e-773">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="d900e-774">Sınırlı alan Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : 2048 bit minimum</span><span class="sxs-lookup"><span data-stu-id="d900e-774">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="d900e-775">Şifre paketi engellenmedi</span><span class="sxs-lookup"><span data-stu-id="d900e-775">Cipher suite not blocked</span></span>

<span data-ttu-id="d900e-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; P-256 eliptik eğrisi &lbrack; `FIPS186` &rbrack; Varsayılan olarak desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d900e-776">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d900e-777">Aşağıdaki örnek, 8000 numaralı bağlantı noktasında HTTP/1.1 ve HTTP/2 bağlantılarına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d900e-777">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d900e-778">Bağlantılar, sağlanan bir sertifikayla TLS ile güvenlidir:</span><span class="sxs-lookup"><span data-stu-id="d900e-778">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="d900e-779">İsteğe bağlı olarak, `IConnectionAdapter` belirli şifrelemeler için bağlantı BAŞıNA TLS el sıkışmaları filtrelemek üzere bir uygulama oluşturun:</span><span class="sxs-lookup"><span data-stu-id="d900e-779">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="d900e-780">*Protokolü yapılandırmadan ayarla*</span><span class="sxs-lookup"><span data-stu-id="d900e-780">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d900e-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>`serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="d900e-781"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d900e-782">Aşağıdaki *appsettings.json* örnekte, Kestrel 'in tüm uç noktaları için varsayılan bir bağlantı protokolü (http/1.1 ve http/2) oluşturulmuştur:</span><span class="sxs-lookup"><span data-stu-id="d900e-782">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="d900e-783">Aşağıdaki yapılandırma dosyası örneği, belirli bir uç nokta için bağlantı protokolü oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d900e-783">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="d900e-784">Yapılandırma tarafından ayarlanan kod geçersiz kılma değerlerinde belirtilen protokoller.</span><span class="sxs-lookup"><span data-stu-id="d900e-784">Protocols specified in code override values set by configuration.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="d900e-785">Libuv aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-785">Libuv transport configuration</span></span>

<span data-ttu-id="d900e-786">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="d900e-786">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d900e-787">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-787">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d900e-788">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="d900e-788">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d900e-789">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d900e-789">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d900e-790">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="d900e-790">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d900e-791">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-791">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d900e-792">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-792">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="d900e-793">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="d900e-793">URL prefixes</span></span>

<span data-ttu-id="d900e-794">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-794">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d900e-795">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-795">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d900e-796">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="d900e-796">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d900e-797">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="d900e-797">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d900e-798">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="d900e-798">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d900e-799">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="d900e-799">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d900e-800">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="d900e-800">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d900e-801">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="d900e-801">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d900e-802">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="d900e-802">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d900e-803">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="d900e-803">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d900e-804">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-804">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d900e-805">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d900e-805">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d900e-806">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="d900e-806">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d900e-807">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-807">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d900e-808">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-808">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d900e-809">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d900e-809">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d900e-810">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="d900e-810">Host filtering</span></span>

<span data-ttu-id="d900e-811">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="d900e-811">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d900e-812">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="d900e-812">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d900e-813">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-813">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d900e-814">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="d900e-814">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d900e-815">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-815">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d900e-816">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-816">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d900e-817">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-817">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d900e-818">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-818">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d900e-819">Ara yazılımı etkinleştirmek için `AllowedHosts` appSettings 'de bir anahtar tanımlayın *appsettings.json* / *. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="d900e-819">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d900e-820">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="d900e-820">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d900e-821">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d900e-821">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d900e-822">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-822">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d900e-823">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-823">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d900e-824">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="d900e-824">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d900e-825">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-825">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d900e-826">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="d900e-826">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="d900e-827">HTTP/1.1 istek boşaltma</span><span class="sxs-lookup"><span data-stu-id="d900e-827">HTTP/1.1 request draining</span></span>

<span data-ttu-id="d900e-828">HTTP bağlantılarının açılması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-828">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="d900e-829">HTTPS için de kaynak kullanımı yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="d900e-829">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="d900e-830">Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-830">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="d900e-831">Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-831">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="d900e-832">Uygulama, `POST` sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü bir istek gibi her zaman istek gövdesini tüketmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-832">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="d900e-833">`POST`Yeniden yönlendirme durumunda:</span><span class="sxs-lookup"><span data-stu-id="d900e-833">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="d900e-834">İstemci, verilerin bir bölümünü zaten göndermiş olabilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="d900e-834">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="d900e-835">Sunucu 301 yanıtını yazar.</span><span class="sxs-lookup"><span data-stu-id="d900e-835">The server writes the 301 response.</span></span>
* <span data-ttu-id="d900e-836">`POST`Önceki istek gövdesinden gelen veriler tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-836">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="d900e-837">Kestrel, istek gövdesini boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-837">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="d900e-838">İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d900e-838">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="d900e-839">Boşaltma işlemi bağlantının yeniden kullanılmasına izin verme ve kalan verilerin boşaltıma süresi arasında bir ilerleme gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="d900e-839">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="d900e-840">Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-840">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="d900e-841">Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-841">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="d900e-842">Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d900e-842">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="d900e-843">Örneğin, istemciler sınırlı verilerin büyük bir düzeyi olabilir, bu nedenle karşıya yüklenen verileri sınırlamak bir öncelik olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-843">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="d900e-844">Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .</span><span class="sxs-lookup"><span data-stu-id="d900e-844">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="d900e-845">Çağırmanın uyarıları vardır `Abort` :</span><span class="sxs-lookup"><span data-stu-id="d900e-845">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="d900e-846">Yeni bağlantı oluşturma yavaş ve pahalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-846">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="d900e-847">İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-847">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="d900e-848">Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-848">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="d900e-849">Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-849">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="d900e-850">Örneğin, `Abort` kötü amaçlı istemciler veri almaya çalışıyorsa `POST` veya istemci kodunda büyük veya çok sayıda isteğe neden olan bir hata olduğunda çağırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-850">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="d900e-851">`Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.</span><span class="sxs-lookup"><span data-stu-id="d900e-851">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="d900e-852">Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-852">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="d900e-853">Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-853">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="d900e-854">Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="d900e-854">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="d900e-855">Beş saniyelik boşaltma zaman aşımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-855">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="d900e-856">Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir.</span><span class="sxs-lookup"><span data-stu-id="d900e-856">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="d900e-857">Ek istek gövdesi veri çerçeveleri yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-857">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="d900e-858">Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanabilmesi ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-858">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="d900e-859">Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.</span><span class="sxs-lookup"><span data-stu-id="d900e-859">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d900e-860">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d900e-860">Additional resources</span></span>

* <span data-ttu-id="d900e-861">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-861">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d900e-862">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-862">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d900e-863">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="d900e-863">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d900e-864">Kestrel, ASP.NET Core için platformlar arası [Web sunucusudur](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d900e-864">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d900e-865">Kestrel, ASP.NET Core proje şablonlarında varsayılan olarak bulunan Web sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-865">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d900e-866">Kestrel aşağıdaki senaryoları destekler:</span><span class="sxs-lookup"><span data-stu-id="d900e-866">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d900e-867">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d900e-867">HTTPS</span></span>
* <span data-ttu-id="d900e-868">[WebSockets](https://github.com/aspnet/websockets) 'i etkinleştirmek için kullanılan donuk yükseltme</span><span class="sxs-lookup"><span data-stu-id="d900e-868">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d900e-869">NGINX 'in arkasında yüksek performans için UNIX Yuvaları</span><span class="sxs-lookup"><span data-stu-id="d900e-869">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="d900e-870">Kestrel, .NET Core 'un desteklediği tüm platformlarda ve sürümlerde desteklenir.</span><span class="sxs-lookup"><span data-stu-id="d900e-870">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d900e-871">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d900e-871">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d900e-872">Ters ara sunucu ile Kestrel ne zaman kullanılır?</span><span class="sxs-lookup"><span data-stu-id="d900e-872">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d900e-873">Kestrel, kendisi veya [Internet Information Services (IIS)](https://www.iis.net/), [NGINX](https://nginx.org)veya [Apache](https://httpd.apache.org/)gibi bir *ters ara sunucu* ile kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-873">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d900e-874">Ters proxy sunucusu, ağdan gelen HTTP isteklerini alır ve Kestrel 'e iletir.</span><span class="sxs-lookup"><span data-stu-id="d900e-874">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d900e-875">Sınır (Internet 'e yönelik) Web sunucusu olarak kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d900e-875">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel, ters proxy sunucusu olmadan doğrudan Internet ile iletişim kurar](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d900e-877">Ters Proxy yapılandırmasında kullanılan Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d900e-877">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel, IIS, NGINX veya Apache gibi bir ters ara sunucu üzerinden Internet ile dolaylı olarak iletişim kurar](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d900e-879">İki yapılandırma de, ters ara sunucu sunucusuyla veya olmadan, desteklenen bir barındırma yapılandırması.</span><span class="sxs-lookup"><span data-stu-id="d900e-879">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d900e-880">Ters proxy sunucusu olmayan bir uç sunucu olarak kullanılan Kestrel, aynı IP ve bağlantı noktasının birden çok işlem arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="d900e-880">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d900e-881">Kestrel bir bağlantı noktasını dinlemek üzere yapılandırıldığında, Kestrel isteklerin üst bilgilerinden bağımsız olarak bu bağlantı noktası için tüm trafiği işler `Host` .</span><span class="sxs-lookup"><span data-stu-id="d900e-881">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d900e-882">Bağlantı noktalarını paylaşabilen bir ters proxy, istekleri benzersiz bir IP ve bağlantı noktası üzerinde Kestrel 'e iletme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-882">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d900e-883">Ters proxy sunucusu gerekli olmasa bile, ters proxy sunucu kullanılması iyi bir seçim olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-883">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d900e-884">Ters proxy:</span><span class="sxs-lookup"><span data-stu-id="d900e-884">A reverse proxy:</span></span>

* <span data-ttu-id="d900e-885">, Barındırdığı uygulamaların açığa çıkarılan genel yüzey alanını sınırlayabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-885">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d900e-886">Ek bir yapılandırma ve savunma katmanı sağlayın.</span><span class="sxs-lookup"><span data-stu-id="d900e-886">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d900e-887">, Mevcut altyapıyla daha iyi tümleşebilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-887">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d900e-888">Yük dengelemeyi ve güvenli iletişim (HTTPS) yapılandırmasını kolaylaştırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-888">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d900e-889">Yalnızca ters proxy sunucusu bir X. 509.440 sertifikası gerektirir ve bu sunucu, düz HTTP kullanarak, iç ağdaki uygulamanın sunucularıyla iletişim kurabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-889">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d900e-890">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d900e-890">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d900e-891">ASP.NET Core uygulamalarında Kestrel kullanma</span><span class="sxs-lookup"><span data-stu-id="d900e-891">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d900e-892">Microsoft. [AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) paketi [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="d900e-892">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d900e-893">ASP.NET Core proje şablonları varsayılan olarak Kestrel kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-893">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d900e-894">*Program.cs*' de, şablon kodu çağırır <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve bu da <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> arka planda çağrı yapılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-894">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="d900e-895">Çağrıldıktan sonra ek yapılandırma sağlamak için `CreateDefaultBuilder` şunu çağırın <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-895">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d900e-896">Ana bilgisayarı hakkında daha fazla bilgi için `CreateDefaultBuilder` , uygulamasının *konak ayarlama* bölümüne bakın <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="d900e-896">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="d900e-897">Kestrel seçenekleri</span><span class="sxs-lookup"><span data-stu-id="d900e-897">Kestrel options</span></span>

<span data-ttu-id="d900e-898">Kestrel Web sunucusu, Internet 'e yönelik dağıtımlarda özellikle yararlı olan kısıtlama yapılandırma seçeneklerine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-898">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d900e-899">Sınıfının özelliğinde kısıtlamaları ayarlayın <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> .</span><span class="sxs-lookup"><span data-stu-id="d900e-899">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d900e-900">`Limits`Özelliği, sınıfının bir örneğini barındırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> .</span><span class="sxs-lookup"><span data-stu-id="d900e-900">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d900e-901">Aşağıdaki örnekler <xref:Microsoft.AspNetCore.Server.Kestrel.Core> ad alanını kullanır:</span><span class="sxs-lookup"><span data-stu-id="d900e-901">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d900e-902">Aşağıdaki örneklerde C# kodunda yapılandırılan Kestrel seçenekleri de bir [yapılandırma sağlayıcısı](xref:fundamentals/configuration/index)kullanılarak ayarlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-902">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d900e-903">Örneğin, dosya yapılandırma sağlayıcısı Kestrel yapılandırmasını bir *appsettings.json* veya appSettings 'ten yükleyebilir *. {1} Environment}. JSON* dosyası:</span><span class="sxs-lookup"><span data-stu-id="d900e-903">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="d900e-904">Aşağıdaki yaklaşımlardan **birini** kullanın:</span><span class="sxs-lookup"><span data-stu-id="d900e-904">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d900e-905">Kestrel 'i yapılandırma `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="d900e-905">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d900e-906">Sınıfına bir örneği `IConfiguration` ekleyin `Startup` .</span><span class="sxs-lookup"><span data-stu-id="d900e-906">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d900e-907">Aşağıdaki örnek, eklenen yapılandırmanın özelliğe atandığını varsayar `Configuration` .</span><span class="sxs-lookup"><span data-stu-id="d900e-907">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d900e-908">İçinde `Startup.ConfigureServices` , `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-908">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="d900e-909">Ana bilgisayarı oluştururken Kestrel yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="d900e-909">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d900e-910">*Program.cs*' de, `Kestrel` yapılandırma bölümünü Kestrel 'in yapılandırmasına yükleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-910">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="d900e-911">Yukarıdaki yaklaşımların her ikisi de herhangi bir [yapılandırma sağlayıcısıyla](xref:fundamentals/configuration/index)çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-911">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d900e-912">Etkin tut zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="d900e-912">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d900e-913">[Etkin tutma zaman aşımını](https://tools.ietf.org/html/rfc7230#section-6.5)alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-913">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d900e-914">Varsayılan olarak 2 dakikadır.</span><span class="sxs-lookup"><span data-stu-id="d900e-914">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="d900e-915">İstemci bağlantıları üst sınırı</span><span class="sxs-lookup"><span data-stu-id="d900e-915">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d900e-916">En fazla eş zamanlı açık TCP bağlantısı sayısı tüm uygulama için aşağıdaki kodla ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-916">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="d900e-917">HTTP veya HTTPS 'den başka bir protokole (örneğin, bir WebSockets isteğinde) yükseltilen bağlantılara yönelik ayrı bir sınır vardır.</span><span class="sxs-lookup"><span data-stu-id="d900e-917">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d900e-918">Bir bağlantı yükseltildikten sonra, bu sınıra göre sayılmaz `MaxConcurrentConnections` .</span><span class="sxs-lookup"><span data-stu-id="d900e-918">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="d900e-919">En fazla bağlantı sayısı, varsayılan olarak sınırsız (null).</span><span class="sxs-lookup"><span data-stu-id="d900e-919">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d900e-920">En büyük istek gövdesi boyutu</span><span class="sxs-lookup"><span data-stu-id="d900e-920">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d900e-921">Varsayılan en büyük istek gövdesi boyutu 30.000.000 bayttır ve bu değer yaklaşık 28,6 MB 'tır.</span><span class="sxs-lookup"><span data-stu-id="d900e-921">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d900e-922">ASP.NET Core MVC uygulamasında sınırı geçersiz kılmak için önerilen yaklaşım, <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> bir eylem yönteminde özniteliğini kullanmaktır:</span><span class="sxs-lookup"><span data-stu-id="d900e-922">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d900e-923">Her istekte uygulama için kısıtlamanın nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d900e-923">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="d900e-924">Ara yazılım içindeki belirli bir istek üzerindeki ayarı geçersiz kılın:</span><span class="sxs-lookup"><span data-stu-id="d900e-924">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d900e-925">Uygulama isteği okumaya başladıktan sonra bir istek üzerindeki sınırı yapılandırırsa, bir özel durum oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="d900e-925">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d900e-926">`IsReadOnly`Özelliğin salt okuma durumunda olup olmadığını belirten bir özellik vardır. Bu, `MaxRequestBodySize` sınırı yapılandırmanın çok geç olduğunu gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-926">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d900e-927">Bir uygulama [ASP.NET Core modülünün](xref:host-and-deploy/aspnet-core-module)arkasında [çalıştırıldığında](xref:host-and-deploy/iis/index#out-of-process-hosting-model) , IIS sınırı zaten ayarladığı için Kestrel 'nin istek gövdesi boyut sınırı devre dışı bırakılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-927">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d900e-928">En az istek gövdesi veri hızı</span><span class="sxs-lookup"><span data-stu-id="d900e-928">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d900e-929">Kestrel bayt/saniye cinsinden belirtilen fiyata ulaşan her saniye sonra denetler.</span><span class="sxs-lookup"><span data-stu-id="d900e-929">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d900e-930">Oran en düşük değerin altına düşerse bağlantı zaman aşımına uğrar. Yetkisiz kullanım süresi, Kestrel 'in istemciye gönderme oranını en düşük süreye kadar artırabilme Bu süre boyunca fiyat denetlenmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-930">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d900e-931">Yetkisiz kullanım süresi, TCP yavaş başlatma nedeniyle başlangıçta verileri yavaş bir hızda gönderen bağlantıların bırakılmasını önlemeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-931">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d900e-932">Varsayılan en düşük oran, 5 saniyelik bir yetkisiz kullanım süresi ile 240 bayt/saniye olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-932">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d900e-933">Yanıt için bir minimum oran da geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-933">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d900e-934">İstek sınırını ayarlamaya yönelik kod ve yanıt sınırı, `RequestBody` `Response` özellik ve arabirim adlarında olduğu gibi aynı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-934">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d900e-935">*Program.cs* içinde en düşük veri hızlarının nasıl yapılandırılacağını gösteren bir örnek aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d900e-935">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="d900e-936">İstek üst bilgileri zaman aşımı</span><span class="sxs-lookup"><span data-stu-id="d900e-936">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d900e-937">Sunucunun istek üst bilgilerini alması için harcadığı en uzun süreyi alır veya ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-937">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d900e-938">Varsayılan değer 30 saniyedir.</span><span class="sxs-lookup"><span data-stu-id="d900e-938">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="d900e-939">Zaman Uyumlu G/Ç</span><span class="sxs-lookup"><span data-stu-id="d900e-939">Synchronous I/O</span></span>

<span data-ttu-id="d900e-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> istek ve yanıt için zaman uyumlu g/ç 'ye izin verilip verilmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="d900e-940"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d900e-941">Varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="d900e-941">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d900e-942">Çok sayıda engelleme zaman uyumlu g/ç işlemi, iş parçacığı havuzuna yol açabilir, bu da uygulamanın yanıt vermemesine neden olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-942">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d900e-943">Yalnızca `AllowSynchronousIO` zaman uyumsuz g/ç desteklemeyen bir kitaplık kullanırken etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d900e-943">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d900e-944">Aşağıdaki örnek, zaman uyumlu g/ç 'yi devre dışı bırakır:</span><span class="sxs-lookup"><span data-stu-id="d900e-944">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="d900e-945">Diğer Kestrel seçenekleri ve limitleri hakkında daha fazla bilgi için bkz.:</span><span class="sxs-lookup"><span data-stu-id="d900e-945">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d900e-946">Uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-946">Endpoint configuration</span></span>

<span data-ttu-id="d900e-947">Varsayılan olarak, ASP.NET Core bağlar:</span><span class="sxs-lookup"><span data-stu-id="d900e-947">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d900e-948">`https://localhost:5001` (bir yerel geliştirme sertifikası varsa)</span><span class="sxs-lookup"><span data-stu-id="d900e-948">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d900e-949">Kullanarak URL 'Leri belirtin:</span><span class="sxs-lookup"><span data-stu-id="d900e-949">Specify URLs using the:</span></span>

* <span data-ttu-id="d900e-950">`ASPNETCORE_URLS` ortam değişkeni.</span><span class="sxs-lookup"><span data-stu-id="d900e-950">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d900e-951">`--urls` komut satırı bağımsız değişkeni.</span><span class="sxs-lookup"><span data-stu-id="d900e-951">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d900e-952">`urls` Ana bilgisayar yapılandırma anahtarı.</span><span class="sxs-lookup"><span data-stu-id="d900e-952">`urls` host configuration key.</span></span>
* <span data-ttu-id="d900e-953">`UseUrls` genişletme yöntemi.</span><span class="sxs-lookup"><span data-stu-id="d900e-953">`UseUrls` extension method.</span></span>

<span data-ttu-id="d900e-954">Bu yaklaşımlar kullanılarak sağlanan değer bir veya daha fazla HTTP ve HTTPS uç noktası olabilir (varsayılan bir sertifika varsa HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d900e-954">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d900e-955">Değeri noktalı virgülle ayrılmış bir liste olarak yapılandırın (örneğin, `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="d900e-955">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d900e-956">Bu yaklaşımlar hakkında daha fazla bilgi için [sunucu URL 'leri](xref:fundamentals/host/web-host#server-urls) ve [geçersiz kılma yapılandırması](xref:fundamentals/host/web-host#override-configuration)bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-956">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d900e-957">Geliştirme sertifikası oluşturuldu:</span><span class="sxs-lookup"><span data-stu-id="d900e-957">A development certificate is created:</span></span>

* <span data-ttu-id="d900e-958">[.NET Core SDK](/dotnet/core/sdk) yüklendiği zaman.</span><span class="sxs-lookup"><span data-stu-id="d900e-958">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d900e-959">[Geliştirme-CERT aracı](xref:aspnetcore-2.1#https) bir sertifika oluşturmak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-959">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d900e-960">Bazı tarayıcılarda yerel geliştirme sertifikasına güvenmek için açık izin verilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="d900e-960">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d900e-961">Proje şablonları, uygulamaları HTTPS üzerinde varsayılan olarak çalışacak şekilde yapılandırır ve [https yeniden yönlendirme ve HSTS desteği](xref:security/enforcing-ssl)içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-961">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d900e-962"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> URL öneklerini ve bağlantı noktalarını Kestrel için yapılandırmak üzere üzerinde çağrı veya Yöntemler.</span><span class="sxs-lookup"><span data-stu-id="d900e-962">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d900e-963">`UseUrls`, `--urls` komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı ve `ASPNETCORE_URLS` ortam değişkeni de çalışır, ancak bu bölümün ilerleyen kısımlarında belirtilen sınırlamalara sahiptir (https uç noktası yapılandırması için varsayılan sertifika kullanılabilir olmalıdır).</span><span class="sxs-lookup"><span data-stu-id="d900e-963">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d900e-964">`KestrelServerOptions` yapılandırmada</span><span class="sxs-lookup"><span data-stu-id="d900e-964">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d900e-965">ConfigureEndpointDefaults Varsayılanları (eylem \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="d900e-965">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d900e-966">`Action`Belirtilen her bir uç nokta için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-966">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d900e-967">`ConfigureEndpointDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="d900e-967">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d900e-968">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-968">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d900e-969">ConfigureHttpsDefaults (eylem \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="d900e-969">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d900e-970">`Action`Her HTTPS uç noktası için çalıştırılacak bir yapılandırma belirtir.</span><span class="sxs-lookup"><span data-stu-id="d900e-970">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d900e-971">`ConfigureHttpsDefaults`Birden çok kez çağırma önceki `Action` s 'yi son belirtilen ile değiştirir `Action` .</span><span class="sxs-lookup"><span data-stu-id="d900e-971">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

> [!NOTE]
> <span data-ttu-id="d900e-972">Çağrılmadan önce çağırarak oluşturulan uç noktalara <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>  <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> varsayılan değer uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-972">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="d900e-973">Yapılandırma (Iconation)</span><span class="sxs-lookup"><span data-stu-id="d900e-973">Configure(IConfiguration)</span></span>

<span data-ttu-id="d900e-974">Bir as girişi alan Kestrel ayarlamak için bir yapılandırma yükleyicisi oluşturur <xref:Microsoft.Extensions.Configuration.IConfiguration> .</span><span class="sxs-lookup"><span data-stu-id="d900e-974">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d900e-975">Yapılandırma, Kestrel için yapılandırma bölümünün kapsamına alınmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-975">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d900e-976">ListenOptions. UseHttps</span><span class="sxs-lookup"><span data-stu-id="d900e-976">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d900e-977">Kestrel 'i HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-977">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d900e-978">`ListenOptions.UseHttps` uzantılardan</span><span class="sxs-lookup"><span data-stu-id="d900e-978">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d900e-979">`UseHttps`: Kestrel 'i varsayılan sertifikayla HTTPS kullanacak şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-979">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d900e-980">Varsayılan sertifika yapılandırılmamışsa bir özel durum oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d900e-980">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="d900e-981">`ListenOptions.UseHttps` parametrelere</span><span class="sxs-lookup"><span data-stu-id="d900e-981">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d900e-982">`filename` , uygulamanın içerik dosyalarını içeren dizine göre bir sertifika dosyasının yolu ve dosya adıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-982">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d900e-983">`password` X. 509.440 sertifika verilerine erişmek için parola gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-983">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d900e-984">`configureOptions` , öğesini `Action` yapılandırmak için kullanılır `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="d900e-984">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d900e-985">Döndürür `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="d900e-985">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d900e-986">`storeName` , sertifikanın yükleneceği sertifika deposudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-986">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d900e-987">`subject` , sertifika için konu adıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-987">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d900e-988">`allowInvalid` geçersiz sertifikaların, otomatik olarak imzalanan sertifikalar gibi göz önünde bulundurulmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="d900e-988">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d900e-989">`location` , sertifikanın yükleneceği mağaza konumudur.</span><span class="sxs-lookup"><span data-stu-id="d900e-989">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d900e-990">`serverCertificate` , X. 509.440 sertifikasıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-990">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d900e-991">Üretimde HTTPS 'nin açıkça yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d900e-991">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d900e-992">En azından, varsayılan bir sertifika sağlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-992">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d900e-993">Daha sonra açıklanan desteklenen yapılandırma:</span><span class="sxs-lookup"><span data-stu-id="d900e-993">Supported configurations described next:</span></span>

* <span data-ttu-id="d900e-994">Yapılandırma yok</span><span class="sxs-lookup"><span data-stu-id="d900e-994">No configuration</span></span>
* <span data-ttu-id="d900e-995">Varsayılan sertifikayı yapılandırmadan Değiştir</span><span class="sxs-lookup"><span data-stu-id="d900e-995">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d900e-996">Koddaki varsayılanları değiştirme</span><span class="sxs-lookup"><span data-stu-id="d900e-996">Change the defaults in code</span></span>

<span data-ttu-id="d900e-997">*Yapılandırma yok*</span><span class="sxs-lookup"><span data-stu-id="d900e-997">*No configuration*</span></span>

<span data-ttu-id="d900e-998">Kestrel tarihinde dinler `http://localhost:5000` ve `https://localhost:5001` (varsayılan bir sertifika varsa).</span><span class="sxs-lookup"><span data-stu-id="d900e-998">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d900e-999">*Varsayılan sertifikayı yapılandırmadan Değiştir*</span><span class="sxs-lookup"><span data-stu-id="d900e-999">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d900e-1000">`CreateDefaultBuilder``Configure(context.Configuration.GetSection("Kestrel"))`Kestrel yapılandırmasını yüklemek için varsayılan olarak çağırır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1000">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d900e-1001">Varsayılan bir HTTPS uygulama ayarları yapılandırma şeması Kestrel için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1001">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d900e-1002">Disk üzerindeki bir dosyadan ya da bir sertifika deposundan kullanılacak URL 'Ler ve Sertifikalar dahil olmak üzere birden çok uç nokta yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1002">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d900e-1003">Aşağıdaki *appsettings.json* örnekte:</span><span class="sxs-lookup"><span data-stu-id="d900e-1003">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d900e-1004">Geçersiz sertifikaların kullanılmasına izin vermek için **Allowwınvalid** `true` ' i ayarlayın (örneğin, otomatik olarak imzalanan sertifikalar).</span><span class="sxs-lookup"><span data-stu-id="d900e-1004">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d900e-1005">Bir sertifika belirtmeyen herhangi bir HTTPS uç noktası (aşağıdaki örnekte bulunan **httpsdefaultcert** ), **Sertifikalar** > **varsayılan** veya geliştirme sertifikası altında tanımlanan sertifikaya geri döner.</span><span class="sxs-lookup"><span data-stu-id="d900e-1005">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="d900e-1006">Herhangi bir sertifika düğümü için **yol** ve **parola** kullanmanın alternatifi sertifika deposu alanlarını kullanarak sertifikayı belirtmektir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1006">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d900e-1007">Örneğin, **sertifika**  >  **varsayılan** sertifikası şu şekilde belirtilebilir:</span><span class="sxs-lookup"><span data-stu-id="d900e-1007">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d900e-1008">Şema notları:</span><span class="sxs-lookup"><span data-stu-id="d900e-1008">Schema notes:</span></span>

* <span data-ttu-id="d900e-1009">Uç nokta adları büyük/küçük harfe duyarlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1009">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d900e-1010">Örneğin, `HTTPS` ve `Https` geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1010">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d900e-1011">`Url`Her uç nokta için parametresi gereklidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1011">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d900e-1012">Bu parametrenin biçimi, en üst düzey `Urls` yapılandırma parametresiyle aynıdır, ancak tek bir değerle sınırlı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-1012">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d900e-1013">Bu uç noktalar, üst düzey yapılandırmada tanımlananlar yerine `Urls` bunlara ekleme yerine bunların yerini alır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1013">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d900e-1014">Kullanılarak kodda tanımlanan uç noktalar `Listen` yapılandırma bölümünde tanımlanan uç noktalar ile birikimlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1014">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d900e-1015">Bu `Certificate` bölüm isteğe bağlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1015">The `Certificate` section is optional.</span></span> <span data-ttu-id="d900e-1016">`Certificate`Bölüm belirtilmemişse, önceki senaryolarda tanımlanan varsayılanlar kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1016">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d900e-1017">Kullanılabilir varsayılan değer yoksa, sunucu bir özel durum oluşturur ve başlayamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-1017">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d900e-1018">Bu `Certificate` bölüm hem **yol** &ndash; **parolasını** hem de **Konu** &ndash; **deposu** sertifikalarını destekler.</span><span class="sxs-lookup"><span data-stu-id="d900e-1018">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d900e-1019">Herhangi bir sayıda uç nokta, bağlantı noktası çakışmalarına neden olmadıkları sürece bu şekilde tanımlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1019">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d900e-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` yapılandırılmış bir `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` uç noktanın ayarlarını tamamlamak için kullanılabilecek bir yöntemi olan bir döndürür:</span><span class="sxs-lookup"><span data-stu-id="d900e-1020">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="d900e-1021">`KestrelServerOptions.ConfigurationLoader` , tarafından sağlana gibi var olan yükleyicisindeki yinelemeye devam etmek için doğrudan erişilebilir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="d900e-1021">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d900e-1022">Her uç noktanın yapılandırma bölümü, `Endpoint` özel ayarların okunabilmesi için yöntemindeki seçeneklerde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1022">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d900e-1023">Birden çok yapılandırma, `options.Configure(context.Configuration.GetSection("{SECTION}"))` başka bir bölümle yeniden çağırarak yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1023">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d900e-1024">Önceki örneklerde açıkça çağrılmadığı takdirde, yalnızca son yapılandırma kullanılır `Load` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1024">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d900e-1025">Metapackage, `Load` varsayılan yapılandırma bölümünün değiştirilmesini sağlayacak şekilde çağırmıyor.</span><span class="sxs-lookup"><span data-stu-id="d900e-1025">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d900e-1026">`KestrelConfigurationLoader``Listen`API ailesini `KestrelServerOptions` `Endpoint` aşırı yükleme olarak yansıtır, bu nedenle kod ve yapılandırma uç noktaları aynı yerde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1026">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d900e-1027">Bu aşırı yüklemeler adları kullanmaz ve yalnızca yapılandırmadan varsayılan ayarları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1027">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d900e-1028">*Koddaki varsayılanları değiştirme*</span><span class="sxs-lookup"><span data-stu-id="d900e-1028">*Change the defaults in code*</span></span>

<span data-ttu-id="d900e-1029">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` `ListenOptions` `HttpsConnectionAdapterOptions` , önceki senaryoda belirtilen varsayılan sertifikayı geçersiz kılma dahil, ve için varsayılan ayarları değiştirmek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1029">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d900e-1030">`ConfigureEndpointDefaults` ve `ConfigureHttpsDefaults` herhangi bir uç nokta yapılandırılmadan önce çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1030">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="d900e-1031">*SNı için Kestrel desteği*</span><span class="sxs-lookup"><span data-stu-id="d900e-1031">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d900e-1032">[Sunucu adı belirtme (SNı)](https://tools.ietf.org/html/rfc6066#section-3) , aynı IP adresi ve bağlantı noktası üzerinde birden fazla etki alanını barındırmak için kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1032">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d900e-1033">SNı 'nin çalışması için, istemci, TLS el sıkışması sırasında güvenli oturum ana bilgisayar adını, sunucunun doğru sertifikayı sağlayabilmesi için gönderir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1033">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d900e-1034">İstemci, TLS anlaşmasını izleyen güvenli oturum sırasında sunucuyla şifreli iletişim için bulunan sertifikayı kullanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1034">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d900e-1035">Kestrel, geri çağırma aracılığıyla SNı destekler `ServerCertificateSelector` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1035">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d900e-1036">Geri çağırma, uygulamanın ana bilgisayar adını incelemesine ve uygun sertifikayı seçmesini sağlamak için bağlantı başına bir kez çağrılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1036">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d900e-1037">SNı desteği şunları gerektirir:</span><span class="sxs-lookup"><span data-stu-id="d900e-1037">SNI support requires:</span></span>

* <span data-ttu-id="d900e-1038">Hedef Framework `netcoreapp2.1` veya sonraki sürümlerde çalışıyor.</span><span class="sxs-lookup"><span data-stu-id="d900e-1038">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d900e-1039">`net461`Veya sonraki sürümlerde, geri çağırma çağrılır, ancak `name` her zaman olur `null` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1039">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d900e-1040">`name`Ayrıca, `null` ISTEMCI, TLS el sıkışmasının ana bilgisayar adı parametresini sağlamıyorsa de olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-1040">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d900e-1041">Tüm Web siteleri aynı Kestrel örneğinde çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1041">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d900e-1042">Kestrel, bir IP adresi ve bağlantı noktasının bir ters proxy olmadan birden çok örnek arasında paylaşılmasını desteklemez.</span><span class="sxs-lookup"><span data-stu-id="d900e-1042">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="connection-logging"></a><span data-ttu-id="d900e-1043">Bağlantı günlüğü</span><span class="sxs-lookup"><span data-stu-id="d900e-1043">Connection logging</span></span>

<span data-ttu-id="d900e-1044"><xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*>Bir bağlantıda bayt düzeyinde iletişim Için hata ayıklama düzeyi günlüklerini yayma çağrısı.</span><span class="sxs-lookup"><span data-stu-id="d900e-1044">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d900e-1045">Bağlantı günlüğü, TLS şifreleme ve proxy 'nin arkasındaki gibi alt düzey iletişimde sorunları gidermeye yardımcı olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-1045">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d900e-1046">`UseConnectionLogging`Daha önce yerleştirilmişse `UseHttps` , şifrelenmiş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1046">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d900e-1047">`UseConnectionLogging`Öğesinden sonra yerleştirilmişse `UseHttps` , şifresi çözülmüş trafik günlüğe kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1047">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d900e-1048">TCP yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="d900e-1048">Bind to a TCP socket</span></span>

<span data-ttu-id="d900e-1049"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Yöntemi BIR TCP yuvasına bağlanır ve bir seçenek lambda X. 509.440 sertifika yapılandırmasına izin verir:</span><span class="sxs-lookup"><span data-stu-id="d900e-1049">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="d900e-1050">Örnek, HTTPS 'yi ile bir uç nokta için yapılandırır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="d900e-1050">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d900e-1051">Belirli uç noktalar için diğer Kestrel ayarlarını yapılandırmak üzere aynı API 'yi kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1051">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d900e-1052">UNIX yuvasına bağlama</span><span class="sxs-lookup"><span data-stu-id="d900e-1052">Bind to a Unix socket</span></span>

<span data-ttu-id="d900e-1053"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*>Aşağıdaki örnekte gösterildiği gibi NGINX ile gelişmiş performans için ile birlikte bir UNIX yuvası dinleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-1053">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

* <span data-ttu-id="d900e-1054">NGINX confiuguration dosyasında, `server`  >  `location`  >  `proxy_pass` girdisini olarak ayarlayın `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1054">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d900e-1055">`{KESTREL SOCKET}` , için belirtilen yuvanın adıdır <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (örneğin, `kestrel-test.sock` Önceki örnekte).</span><span class="sxs-lookup"><span data-stu-id="d900e-1055">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d900e-1056">Yuvanın NGINX tarafından yazılabilir olduğundan emin olun (örneğin, `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="d900e-1056">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="d900e-1057">Bağlantı noktası 0</span><span class="sxs-lookup"><span data-stu-id="d900e-1057">Port 0</span></span>

<span data-ttu-id="d900e-1058">Bağlantı noktası numarası `0` belirtildiğinde, Kestrel dinamik olarak kullanılabilir bir bağlantı noktasına bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1058">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d900e-1059">Aşağıdaki örnek, çalışma zamanında Kestrel gerçekte hangi bağlantı noktasının gerçekten bağlandığını nasıl belirleyeceğini göstermektedir:</span><span class="sxs-lookup"><span data-stu-id="d900e-1059">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d900e-1060">Uygulama çalıştırıldığında konsol penceresi çıkışı, uygulamanın erişilebileceği dinamik bağlantı noktasını gösterir:</span><span class="sxs-lookup"><span data-stu-id="d900e-1060">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d900e-1061">Sınırlamalar</span><span class="sxs-lookup"><span data-stu-id="d900e-1061">Limitations</span></span>

<span data-ttu-id="d900e-1062">Aşağıdaki yaklaşımlar ile uç noktaları yapılandırın:</span><span class="sxs-lookup"><span data-stu-id="d900e-1062">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d900e-1063">`--urls` komut satırı bağımsız değişkeni</span><span class="sxs-lookup"><span data-stu-id="d900e-1063">`--urls` command-line argument</span></span>
* <span data-ttu-id="d900e-1064">`urls` Ana bilgisayar yapılandırma anahtarı</span><span class="sxs-lookup"><span data-stu-id="d900e-1064">`urls` host configuration key</span></span>
* <span data-ttu-id="d900e-1065">`ASPNETCORE_URLS` ortam değişkeni</span><span class="sxs-lookup"><span data-stu-id="d900e-1065">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d900e-1066">Bu yöntemler, kodun Kestrel dışındaki sunucularla çalışmasını sağlamak için yararlıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1066">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d900e-1067">Ancak, aşağıdaki sınırlamalara dikkat edin:</span><span class="sxs-lookup"><span data-stu-id="d900e-1067">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d900e-1068">HTTPS uç noktası yapılandırmasında varsayılan bir sertifika sağlanmadığı sürece HTTPS bu yaklaşımlar ile kullanılamaz (örneğin, `KestrelServerOptions` Bu konunun önceki kısımlarında gösterildiği gibi yapılandırma veya yapılandırma dosyası kullanılıyor).</span><span class="sxs-lookup"><span data-stu-id="d900e-1068">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d900e-1069">Hem hem de `Listen` `UseUrls` yaklaşımları aynı anda kullanıldığında, uç noktalar `Listen` `UseUrls` uç noktaları geçersiz kılar.</span><span class="sxs-lookup"><span data-stu-id="d900e-1069">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d900e-1070">IIS uç nokta yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-1070">IIS endpoint configuration</span></span>

<span data-ttu-id="d900e-1071">IIS kullanırken, IIS geçersiz kılma bağlamaları için URL bağlamaları veya ya da tarafından ayarlanır `Listen` `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1071">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d900e-1072">Daha fazla bilgi için [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1072">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="libuv-transport-configuration"></a><span data-ttu-id="d900e-1073">Libuv aktarım yapılandırması</span><span class="sxs-lookup"><span data-stu-id="d900e-1073">Libuv transport configuration</span></span>

<span data-ttu-id="d900e-1074">ASP.NET Core 2,1 sürümü ile Kestrel 'in varsayılan taşıması artık libuv ' d i temel değildir ancak bunun yerine yönetilen yuvaları temel alır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1074">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d900e-1075">Bu, çağrıyı yapan ve aşağıdaki paketlerden birine bağlı olan ASP.NET Core 2,0 2,1 uygulamalarının önemli bir değişikliği olur <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-1075">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d900e-1076">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (doğrudan paket başvurusu)</span><span class="sxs-lookup"><span data-stu-id="d900e-1076">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d900e-1077">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d900e-1077">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d900e-1078">Libuv kullanımını gerektiren projeler için:</span><span class="sxs-lookup"><span data-stu-id="d900e-1078">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d900e-1079">Uygulamanın proje dosyasına [Microsoft. AspNetCore. Server. Kestrel. Transport. libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) paketi için bir bağımlılık ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d900e-1079">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d900e-1080">Çağrı <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-1080">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="d900e-1081">URL önekleri</span><span class="sxs-lookup"><span data-stu-id="d900e-1081">URL prefixes</span></span>

<span data-ttu-id="d900e-1082">`UseUrls`, `--urls` Komut satırı bağımsız değişkeni, `urls` ana bilgisayar yapılandırma anahtarı veya `ASPNETCORE_URLS` ortam değişkeni kullanılırken, URL önekleri aşağıdaki biçimlerden birinde olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1082">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d900e-1083">Yalnızca HTTP URL ön ekleri geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1083">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d900e-1084">Kestrel, kullanılarak URL bağlamaları yapılandırılırken HTTPS 'YI desteklemez `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1084">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d900e-1085">Bağlantı noktası numarası olan IPv4 adresi</span><span class="sxs-lookup"><span data-stu-id="d900e-1085">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d900e-1086">`0.0.0.0` Tüm IPv4 adreslerine bağlanan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="d900e-1086">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d900e-1087">Bağlantı noktası numarasına sahip IPv6 adresi</span><span class="sxs-lookup"><span data-stu-id="d900e-1087">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d900e-1088">`[::]` , IPv4 'un IPv6 eşdeğeridir `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1088">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d900e-1089">Bağlantı noktası numarası olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="d900e-1089">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d900e-1090">, Ve ana bilgisayar adları `*` `+` özel değildir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1090">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d900e-1091">Geçerli bir IP adresi olarak tanınmayan veya `localhost` tüm IPv4 ve IPv6 IP 'lerine bağlanan her şey.</span><span class="sxs-lookup"><span data-stu-id="d900e-1091">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d900e-1092">Farklı ana bilgisayar adlarını aynı bağlantı noktasında farklı ASP.NET Core uygulamalarına bağlamak için, [HTTP.sys](xref:fundamentals/servers/httpsys) veya IIS, NGINX veya Apache gibi bir ters proxy sunucusunu kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1092">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d900e-1093">Ters Proxy yapılandırmasında barındırma, [konak filtrelemeyi](#host-filtering)gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1093">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d900e-1094">Port numarası `localhost` veya bağlantı noktası numarası ile geri döngü IP 'si olan ana bilgisayar adı</span><span class="sxs-lookup"><span data-stu-id="d900e-1094">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d900e-1095">Belirtildiğinde `localhost` , Kestrel hem IPv4 hem de IPv6 geri döngü arabirimlerini bağlamaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1095">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d900e-1096">İstenen bağlantı noktası, herhangi bir geri döngü arabiriminde başka bir hizmet tarafından kullanılıyorsa, Kestrel başlatılamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-1096">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d900e-1097">Herhangi bir geri döngü arabirimi başka bir nedenle kullanılamıyorsa (genellikle IPv6 desteklenmediği için), Kestrel bir uyarı kaydeder.</span><span class="sxs-lookup"><span data-stu-id="d900e-1097">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d900e-1098">Konak filtreleme</span><span class="sxs-lookup"><span data-stu-id="d900e-1098">Host filtering</span></span>

<span data-ttu-id="d900e-1099">Kestrel gibi önekleri temel alarak yapılandırmayı desteklese `http://example.com:5000` de, Kestrel büyük ölçüde ana bilgisayar adını yoksayar.</span><span class="sxs-lookup"><span data-stu-id="d900e-1099">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d900e-1100">Ana bilgisayar `localhost` , geri döngü adreslerine bağlama için kullanılan özel bir durumdur.</span><span class="sxs-lookup"><span data-stu-id="d900e-1100">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d900e-1101">Açık IP adresi dışındaki tüm ana bilgisayar tüm genel IP adreslerine bağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1101">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d900e-1102">`Host` Üstbilgiler doğrulanmadı.</span><span class="sxs-lookup"><span data-stu-id="d900e-1102">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d900e-1103">Geçici bir çözüm olarak, ana bilgisayar filtreleme ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1103">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d900e-1104">Ana bilgisayar filtreleme ara yazılımı, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 veya 2,2) ' de yer alan [Microsoft. Aspnetcore. hostfiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1104">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d900e-1105">Ara yazılım tarafından eklenir <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve şunları çağırır <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="d900e-1105">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d900e-1106">Ana bilgisayar filtreleme ara yazılımı varsayılan olarak devre dışıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1106">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d900e-1107">Ara yazılımı etkinleştirmek için `AllowedHosts` appSettings 'de bir anahtar tanımlayın *appsettings.json* / *. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="d900e-1107">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d900e-1108">Değer, bağlantı noktası numaraları olmayan ana bilgisayar adlarının noktalı virgülle ayrılmış listesidir:</span><span class="sxs-lookup"><span data-stu-id="d900e-1108">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d900e-1109">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d900e-1109">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d900e-1110">[Iletilen üstbilgiler ara yazılımı](xref:host-and-deploy/proxy-load-balancer) da bir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> seçenek içerir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1110">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d900e-1111">İletilen üstbilgiler ara yazılımı ve ana bilgisayar filtreleme ara yazılımı, farklı senaryolar için benzer işlevlere sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1111">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d900e-1112">`AllowedHosts`Iletilen üstbilgiler ara yazılımı ile, `Host` istekler ters bir ara sunucu veya yük dengeleyici ile iletilirken üst bilgi korunurken, bu işlem için uygun bir ayar vardır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1112">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d900e-1113">`AllowedHosts`Ana bilgisayar filtreleme ara yazılımı ile ayarlama, Kestrel herkese açık bir uç sunucu olarak veya `Host` üst bilgi doğrudan iletildiğinde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1113">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d900e-1114">Iletilen üstbilgiler ara yazılımı hakkında daha fazla bilgi için bkz <xref:host-and-deploy/proxy-load-balancer> ..</span><span class="sxs-lookup"><span data-stu-id="d900e-1114">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="http11-request-draining"></a><span data-ttu-id="d900e-1115">HTTP/1.1 istek boşaltma</span><span class="sxs-lookup"><span data-stu-id="d900e-1115">HTTP/1.1 request draining</span></span>

<span data-ttu-id="d900e-1116">HTTP bağlantılarının açılması zaman alabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1116">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="d900e-1117">HTTPS için de kaynak kullanımı yoğun bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1117">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="d900e-1118">Bu nedenle Kestrel, HTTP/1.1 protokolü başına bağlantıları yeniden kullanmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1118">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="d900e-1119">Bağlantının yeniden kullanılmasına izin vermek için bir istek gövdesi tam olarak tüketilmelidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1119">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="d900e-1120">Uygulama, `POST` sunucunun yeniden yönlendirme veya 404 yanıtı döndürdüğü bir istek gibi her zaman istek gövdesini tüketmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-1120">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="d900e-1121">`POST`Yeniden yönlendirme durumunda:</span><span class="sxs-lookup"><span data-stu-id="d900e-1121">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="d900e-1122">İstemci, verilerin bir bölümünü zaten göndermiş olabilir `POST` .</span><span class="sxs-lookup"><span data-stu-id="d900e-1122">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="d900e-1123">Sunucu 301 yanıtını yazar.</span><span class="sxs-lookup"><span data-stu-id="d900e-1123">The server writes the 301 response.</span></span>
* <span data-ttu-id="d900e-1124">`POST`Önceki istek gövdesinden gelen veriler tam olarak okunana kadar bağlantı yeni bir istek için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-1124">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="d900e-1125">Kestrel, istek gövdesini boşaltmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1125">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="d900e-1126">İstek gövdesini boşaltma işlemi işlemeden verileri okuma ve atma anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1126">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="d900e-1127">Boşaltma işlemi bağlantının yeniden kullanılmasına izin verme ve kalan verilerin boşaltıma süresi arasında bir ilerleme gerçekleştirir:</span><span class="sxs-lookup"><span data-stu-id="d900e-1127">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="d900e-1128">Boşaltma, yapılandırılabilir olmayan beş saniyelik bir zaman aşımına sahip olur.</span><span class="sxs-lookup"><span data-stu-id="d900e-1128">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="d900e-1129">Veya üstbilgisi tarafından belirtilen tüm veriler `Content-Length` `Transfer-Encoding` zaman aşımından önce okunmadıysa bağlantı kapatılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1129">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="d900e-1130">Bazen, yanıtı yazmadan önce veya sonra isteği hemen sonlandırmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d900e-1130">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="d900e-1131">Örneğin, istemciler sınırlı verilerin büyük bir düzeyi olabilir, bu nedenle karşıya yüklenen verileri sınırlamak bir öncelik olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1131">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="d900e-1132">Bu tür durumlarda bir isteği sonlandırmak için bir denetleyici, sayfa veya ara yazılım aracılığıyla [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) ' ı çağırın Razor .</span><span class="sxs-lookup"><span data-stu-id="d900e-1132">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="d900e-1133">Çağırmanın uyarıları vardır `Abort` :</span><span class="sxs-lookup"><span data-stu-id="d900e-1133">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="d900e-1134">Yeni bağlantı oluşturma yavaş ve pahalı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1134">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="d900e-1135">İstemcinin bağlantı kapanmadan önce yanıtı okuduğunuzdan emin olmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-1135">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="d900e-1136">Çağırma `Abort` , yaygın hatalara değil önemli hata durumları için nadir ve ayrılmış olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1136">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="d900e-1137">Yalnızca `Abort` belirli bir sorunun çözülmesi gerektiğinde çağırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1137">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="d900e-1138">Örneğin, `Abort` kötü amaçlı istemciler veri almaya çalışıyorsa `POST` veya istemci kodunda büyük veya çok sayıda isteğe neden olan bir hata olduğunda çağırın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1138">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="d900e-1139">`Abort`HTTP 404 (bulunamadı) gibi yaygın hata durumları için çağrı yapmayın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1139">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="d900e-1140">Çağrılmadan önce [HttpResponse. tamamlana eşitlemesini](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) çağırmak `Abort` Sunucunun yanıtı yazmayı tamamlamasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d900e-1140">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="d900e-1141">Ancak, istemci davranışı tahmin edilebilir değildir ve bağlantı durdurulmadan önce yanıtı okuyamayabilir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1141">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="d900e-1142">Bu işlem HTTP/2 için farklıdır çünkü protokol, bağlantıyı kapatmadan bağımsız istek akışlarını iptal etme işlemini destekler.</span><span class="sxs-lookup"><span data-stu-id="d900e-1142">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="d900e-1143">Beş saniyelik boşaltma zaman aşımı uygulanmaz.</span><span class="sxs-lookup"><span data-stu-id="d900e-1143">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="d900e-1144">Yanıt tamamlandıktan sonra herhangi bir okunmamış istek gövdesi verisi varsa, sunucu bir HTTP/2 RST çerçevesi gönderir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1144">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="d900e-1145">Ek istek gövdesi veri çerçeveleri yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="d900e-1145">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="d900e-1146">Mümkünse, istemcilerin [Beklenen: 100-Continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) istek üst bilgisini kullanabilmesi ve istek gövdesini göndermeden önce sunucunun yanıt vermesini beklemek daha iyidir.</span><span class="sxs-lookup"><span data-stu-id="d900e-1146">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="d900e-1147">Bu, istemciye gereksiz verileri göndermeden önce yanıtı İnceleme ve iptal etme olanağı sunar.</span><span class="sxs-lookup"><span data-stu-id="d900e-1147">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d900e-1148">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d900e-1148">Additional resources</span></span>

* <span data-ttu-id="d900e-1149">Linux üzerinde UNIX yuvaları kullanırken, yuva uygulama kapatılırken otomatik olarak silinmez.</span><span class="sxs-lookup"><span data-stu-id="d900e-1149">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d900e-1150">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/14134)bakın.</span><span class="sxs-lookup"><span data-stu-id="d900e-1150">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d900e-1151">RFC 7230: Ileti sözdizimi ve yönlendirme (Bölüm 5,4: Ana bilgisayar)</span><span class="sxs-lookup"><span data-stu-id="d900e-1151">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)

::: moniker-end