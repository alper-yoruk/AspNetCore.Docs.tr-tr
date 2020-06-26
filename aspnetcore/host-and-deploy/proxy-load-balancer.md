---
title: Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma
author: rick-anderson
description: Genellikle önemli istek bilgilerini gizleyen ara sunucu ve yük dengeleyiciler arkasında barındırılan uygulamalar için yapılandırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: aab0ec0d142d840ae2b480add9e0416fb27e378e
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85403917"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="993fa-103">Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="993fa-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="993fa-104">[Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="993fa-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="993fa-105">ASP.NET Core için Önerilen yapılandırmada, uygulama IIS/ASP. NET Core modülü, NGINX veya Apache kullanılarak barındırılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="993fa-106">Proxy sunucuları, yük dengeleyiciler ve diğer ağ gereçleri, genellikle istek hakkında uygulamaya ulaşmadan önce bu bilgileri gizlemediğinde:</span><span class="sxs-lookup"><span data-stu-id="993fa-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="993fa-107">HTTPS istekleri HTTP üzerinden proxy olduğunda, özgün düzen (HTTPS) kaybolur ve bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="993fa-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="993fa-108">Bir uygulama, Internet veya şirket ağında gerçek kaynağını değil, ara sunucudan bir istek aldığından, kaynak istemci IP adresinin de bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="993fa-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="993fa-109">Bu bilgiler, istek işlemede, örneğin yeniden yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konum gibi önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="993fa-110">İletilen üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="993fa-110">Forwarded headers</span></span>

<span data-ttu-id="993fa-111">Kurala göre, proxy 'leri HTTP başlıklarında iletme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="993fa-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="993fa-112">Üst bilgi</span><span class="sxs-lookup"><span data-stu-id="993fa-112">Header</span></span> | <span data-ttu-id="993fa-113">Açıklama</span><span class="sxs-lookup"><span data-stu-id="993fa-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="993fa-114">X-Iletilmiş-Için</span><span class="sxs-lookup"><span data-stu-id="993fa-114">X-Forwarded-For</span></span> | <span data-ttu-id="993fa-115">, İsteği başlatan istemciyle ilgili bilgileri ve bir proxy zincirinde sonraki proxy 'leri barındırır.</span><span class="sxs-lookup"><span data-stu-id="993fa-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="993fa-116">Bu parametre IP adresleri (ve isteğe bağlı olarak bağlantı noktası numaraları) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="993fa-117">Bir ara sunucu zincirinde, ilk parametre isteğin ilk yaptığı istemciyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="993fa-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="993fa-118">Sonraki proxy tanımlayıcıları izler.</span><span class="sxs-lookup"><span data-stu-id="993fa-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="993fa-119">Zincirdeki son proxy, Parametreler listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="993fa-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="993fa-120">Son proxy 'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanında uzak IP adresi olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="993fa-121">X-Iletilen-proto</span><span class="sxs-lookup"><span data-stu-id="993fa-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="993fa-122">Kaynak düzenin değeri (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="993fa-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="993fa-123">Bu değer, istek birden çok proxy 'ye geçen bir düzen listesi de olabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="993fa-124">X-Iletilen-konak</span><span class="sxs-lookup"><span data-stu-id="993fa-124">X-Forwarded-Host</span></span> | <span data-ttu-id="993fa-125">Ana bilgisayar üst bilgisi alanının özgün değeri.</span><span class="sxs-lookup"><span data-stu-id="993fa-125">The original value of the Host header field.</span></span> <span data-ttu-id="993fa-126">Genellikle, proxy 'ler ana bilgisayar üst bilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="993fa-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="993fa-127">Proxy 'nin konak üstbilgilerini doğrulamadığı veya kısıtlayamayacağı sistemleri etkileyen ayrıcalık yükselmesi güvenlik açığı hakkında bilgi için bkz. [Microsoft Güvenlik Danışmanlığı CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) .</span><span class="sxs-lookup"><span data-stu-id="993fa-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="993fa-128">[Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki Iletilen üstbilgiler ara yazılımı, bu üst bilgileri okur ve ilgili alanları üzerinde doldurur <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="993fa-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="993fa-129">Ara yazılım güncelleştirmeleri:</span><span class="sxs-lookup"><span data-stu-id="993fa-129">The middleware updates:</span></span>

* <span data-ttu-id="993fa-130">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="993fa-131">Ek ayarlar, ara yazılımın nasıl ayarlanacağını etkiler `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="993fa-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="993fa-132">Ayrıntılar için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="993fa-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="993fa-133">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="993fa-134">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="993fa-135">İletilen üstbilgiler ara yazılımı [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="993fa-136">Varsayılan ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="993fa-136">The default settings are:</span></span>

* <span data-ttu-id="993fa-137">Uygulama ve isteklerin kaynağı arasında yalnızca *bir ara sunucu* vardır.</span><span class="sxs-lookup"><span data-stu-id="993fa-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="993fa-138">Bilinen proxy 'ler ve bilinen ağlar için yalnızca geri döngü adresleri yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="993fa-139">İletilen üstbilgiler ve olarak adlandırılır `X-Forwarded-For` `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="993fa-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="993fa-140">Tüm ağ gereçleri `X-Forwarded-For` `X-Forwarded-Proto` ek yapılandırma olmadan ve üst bilgilerini eklemez.</span><span class="sxs-lookup"><span data-stu-id="993fa-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="993fa-141">Proxy istekler uygulamaya ulaştığında bu üst bilgileri içermiyorsa, Gereç üreticinizin kılavuzuna başvurun.</span><span class="sxs-lookup"><span data-stu-id="993fa-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="993fa-142">Gereç, ve dışındaki farklı üstbilgi adlarını kullanıyorsa `X-Forwarded-For` `X-Forwarded-Proto` , <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini gereç tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="993fa-143">Daha fazla bilgi için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) ve [farklı üstbilgi adları kullanan bir proxy için yapılandırma](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="993fa-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="993fa-144">IIS/IIS Express ve ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="993fa-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="993fa-145">İletilen üstbilgiler ara yazılımı, uygulama IIS 'nin arkasında ve ASP.NET Core modülünden sonra [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) barındırılıyorsa [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="993fa-146">İletilen üstbilgiler ara yazılımı, iletilen üst bilgilerle (örneğin, [IP aldatmacası](https://www.iplocation.net/ip-spoofing)) güven sorunları nedeniyle ASP.NET Core modülüne özgü kısıtlanmış bir yapılandırmaya sahip olan ara yazılım ardışık düzeninde ilk olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="993fa-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="993fa-147">Ara yazılım, `X-Forwarded-For` ve üst bilgilerini iletmek üzere yapılandırılmıştır `X-Forwarded-Proto` ve tek bir localhost proxy ile kısıtlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="993fa-148">Ek yapılandırma gerekliyse, [Iletilen üstbilgiler ara yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="993fa-149">Diğer proxy sunucu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="993fa-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="993fa-150">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında [IIS tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 'nin kullanılması dışında, iletilen üstbilgiler ara yazılımı varsayılan olarak etkinleştirilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="993fa-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="993fa-151">İletilen üstbilgileri ile işlemek için bir uygulamanın yönlendirilmiş üst bilgi ara yazılımı etkinleştirilmelidir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="993fa-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="993fa-152">Ara yazılım için Hayır belirtilmemişse, ara yazılımı etkinleştirdikten sonra <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> varsayılan [Forwardedheadersoptions. Forwardedheaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) , [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dir.</span><span class="sxs-lookup"><span data-stu-id="993fa-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="993fa-153">İle ara yazılımı, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` içindeki ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="993fa-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span>

<a name="fhmo"></a>

### <a name="forwarded-headers-middleware-order"></a><span data-ttu-id="993fa-154">İletilen üstbilgiler ara yazılım sırası</span><span class="sxs-lookup"><span data-stu-id="993fa-154">Forwarded Headers Middleware order</span></span>

<span data-ttu-id="993fa-155">İletilen üstbilgiler ara yazılımı, diğer ara yazılım öncesinde çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993fa-155">Forwarded Headers Middleware should run before other middleware.</span></span> <span data-ttu-id="993fa-156">Bu sıralama, iletilen üst bilgi bilgilerine bağlı olan ara yazılımın işleme için üst bilgi değerlerini kullanmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="993fa-156">This ordering ensures that the middleware relying on forwarded headers information can consume the header values for processing.</span></span> <span data-ttu-id="993fa-157">İletilen üstbilgiler ara yazılımı tanılama ve hata işleme sonrasında çalıştırılabilir, ancak çağrılmadan önce çalıştırılması gerekir `UseHsts` :</span><span class="sxs-lookup"><span data-stu-id="993fa-157">Forwarded Headers Middleware can run after diagnostics and error handling, but it must be be run before calling `UseHsts`:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup.cs?name=snippet&highlight=13-17,25,30)]

<span data-ttu-id="993fa-158">Alternatif olarak, `UseForwardedHeaders` Tanılama öncesinde çağırın:</span><span class="sxs-lookup"><span data-stu-id="993fa-158">Alternatively, call `UseForwardedHeaders` before diagnostics:</span></span>

[!code-csharp[](~/host-and-deploy/proxy-load-balancer/3.1samples/Startup2.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="993fa-159">Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `Startup.ConfigureServices` veya doğrudan genişletme yöntemine belirtilmemişse <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , iletmek Için varsayılan üstbilgiler [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="993fa-159">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="993fa-160"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Özelliğin, iletmek için üst bilgilerle yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="993fa-160">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="993fa-161">NGINX yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993fa-161">Nginx configuration</span></span>

<span data-ttu-id="993fa-162">`X-Forwarded-For`Ve `X-Forwarded-Proto` üst bilgilerini iletmek için bkz <xref:host-and-deploy/linux-nginx#configure-nginx> ..</span><span class="sxs-lookup"><span data-stu-id="993fa-162">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="993fa-163">Daha fazla bilgi için bkz. [NGINX: iletilen üstbilgiyi kullanma](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="993fa-163">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="993fa-164">Apache yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993fa-164">Apache configuration</span></span>

<span data-ttu-id="993fa-165">`X-Forwarded-For`otomatik olarak eklenir (bkz. [Apache modülü mod_proxy: ters proxy Istek üstbilgileri](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="993fa-165">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="993fa-166">Üstbilgiyi iletme hakkında daha fazla bilgi için `X-Forwarded-Proto` bkz <xref:host-and-deploy/linux-apache#configure-apache> ..</span><span class="sxs-lookup"><span data-stu-id="993fa-166">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="993fa-167">İletilen üstbilgiler ara yazılım seçenekleri</span><span class="sxs-lookup"><span data-stu-id="993fa-167">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="993fa-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen üstbilgiler ara yazılımı davranışını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="993fa-168"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="993fa-169">Aşağıdaki örnek varsayılan değerleri değiştirir:</span><span class="sxs-lookup"><span data-stu-id="993fa-169">The following example changes the default values:</span></span>

* <span data-ttu-id="993fa-170">İletilen üst bilgilerdeki giriş sayısını sınırlayın `2` .</span><span class="sxs-lookup"><span data-stu-id="993fa-170">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="993fa-171">Bilinen bir proxy adresi ekleyin `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="993fa-171">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="993fa-172">İletilen üst bilgi adını varsayılan değerinden olarak değiştirin `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="993fa-172">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="993fa-173">Seçenek</span><span class="sxs-lookup"><span data-stu-id="993fa-173">Option</span></span> | <span data-ttu-id="993fa-174">Açıklama</span><span class="sxs-lookup"><span data-stu-id="993fa-174">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="993fa-175">Ana bilgisayarları üst bilgiyle `X-Forwarded-Host` belirtilen değerlerle sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="993fa-175">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="993fa-176">Değerler, Ordinal-Ignore-Case kullanılarak karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-176">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="993fa-177">Bağlantı noktası numaraları dışlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993fa-177">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="993fa-178">Liste boşsa, tüm konaklara izin verilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-178">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="993fa-179">Üst düzey joker karakter `*` boş olmayan tüm konaklara izin verir.</span><span class="sxs-lookup"><span data-stu-id="993fa-179">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="993fa-180">Alt etki alanı Joker karakterlere izin verilir ancak kök etki alanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="993fa-180">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="993fa-181">Örneğin, `*.contoso.com` `foo.contoso.com` kök etki alanını değil, alt etki alanıyla eşleşir `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="993fa-181">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="993fa-182">Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [puni koduna](https://tools.ietf.org/html/rfc3492) dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="993fa-182">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="993fa-183">[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı ayraçları içermeli ve [geleneksel biçimde](https://tools.ietf.org/html/rfc4291#section-2.2) olmalıdır (örneğin, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-183">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="993fa-184">IPv6 adresleri, farklı biçimler arasında mantıksal eşitlik denetimi için özel değildir ve hiçbir kurallı kullanım yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="993fa-184">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="993fa-185">İzin verilen konaklar kısıtlanamaması, bir saldırganın hizmet tarafından oluşturulan bağlantıları sızdırmasına izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-185">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="993fa-186">Varsayılan değer boş `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="993fa-186">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="993fa-187">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-187">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="993fa-188">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-For` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-188">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="993fa-189">Varsayılan değer: `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="993fa-189">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="993fa-190">Hangi ileticilerin işleneceğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="993fa-190">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="993fa-191">Uygulanan alanların listesi için [Forwardedheaders numaralandırması](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-191">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="993fa-192">Bu özelliğe atanan tipik değerler şunlardır `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="993fa-192">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="993fa-193">Varsayılan değer [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="993fa-193">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="993fa-194">[Forwardedheadersdefaults varsayılan. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-194">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="993fa-195">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Host` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-195">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="993fa-196">Varsayılan değer: `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="993fa-196">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="993fa-197">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-197">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="993fa-198">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Proto` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-198">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="993fa-199">Varsayılan değer: `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="993fa-199">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="993fa-200">İşlenen üst bilgilerdeki giriş sayısını sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="993fa-200">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="993fa-201">`null`Sınırı devre dışı bırakmak için olarak ayarlayın, ancak bu yalnızca `KnownProxies` veya `KnownNetworks` yapılandırılmışsa yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993fa-201">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="993fa-202">Değer olmayan bir `null` önlem, hatalı yapılandırılmış proxy 'lerde ve ağdaki yan kanallardan gelen kötü amaçlı isteklere karşı koruma sağlamak için bir önlem (garanti değil) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-202">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="993fa-203">İletilen üstbilgiler ara yazılımı, üst bilgileri sağdan sola doğru sırada işler.</span><span class="sxs-lookup"><span data-stu-id="993fa-203">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="993fa-204">Varsayılan değer ( `1` ) kullanılırsa, değeri artmadığı müddetçe yalnızca üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="993fa-204">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="993fa-205">Varsayılan değer: `1`.</span><span class="sxs-lookup"><span data-stu-id="993fa-205">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="993fa-206">İletilen üstbilgileri kabul etmek için bilinen ağların adres aralıkları.</span><span class="sxs-lookup"><span data-stu-id="993fa-206">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="993fa-207">Sınıfsız Etki alanları arası yönlendirme (CıDR) gösterimini kullanarak IP aralıkları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-207">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="993fa-208">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-208">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="993fa-209">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="993fa-209">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="993fa-210">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-210">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="993fa-211">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-211">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="993fa-212">Varsayılan, `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> için tek bir giriş içeren bir> `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="993fa-212">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="993fa-213">İletilen üstbilgileri kabul etmek için bilinen proxy 'lerin adresleri.</span><span class="sxs-lookup"><span data-stu-id="993fa-213">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="993fa-214">`KnownProxies`Tam IP adresi eşleşmelerini belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-214">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="993fa-215">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-215">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="993fa-216">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="993fa-216">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="993fa-217">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-217">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="993fa-218">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-218">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="993fa-219">Varsayılan, `IList` \<<xref:System.Net.IPAddress> için tek bir giriş içeren bir> `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="993fa-219">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="993fa-220">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="993fa-221">Varsayılan değer: `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="993fa-221">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="993fa-222">[Forwardedheadersdefaults varsayılan. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-222">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="993fa-223">Varsayılan değer: `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="993fa-223">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="993fa-224">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-224">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="993fa-225">Varsayılan değer: `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="993fa-225">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="993fa-226">Bilgi işlem sayısının, işlenmekte olan [Forwardedheadersoptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitlenmiş olmasını gerektir.</span><span class="sxs-lookup"><span data-stu-id="993fa-226">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="993fa-227">ASP.NET Core 1. x içindeki varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="993fa-227">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="993fa-228">ASP.NET Core 2,0 veya sonraki sürümlerde varsayılan değer `false` .</span><span class="sxs-lookup"><span data-stu-id="993fa-228">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="993fa-229">Senaryolar ve kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="993fa-229">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="993fa-230">İletilen üstbilgiler eklemek mümkün olmadığında ve tüm istekler güvenlidir</span><span class="sxs-lookup"><span data-stu-id="993fa-230">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="993fa-231">Bazı durumlarda, iletilen üstbilgileri uygulamaya yönelik isteklere eklemek mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-231">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="993fa-232">Proxy tüm genel dış isteklerin HTTPS olduğunu zortacaktır, Düzen `Startup.Configure` herhangi bir tür ara yazılım kullanılmadan önce içinde el ile ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="993fa-232">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="993fa-233">Bu kod bir geliştirme veya hazırlama ortamındaki ortam değişkeniyle veya diğer yapılandırma ayarıyla devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-233">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="993fa-234">Yol tabanı ve istek yolunu değiştiren proxy 'ler ile uğraşın</span><span class="sxs-lookup"><span data-stu-id="993fa-234">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="993fa-235">Bazı proxy 'ler yolu bozulmadan, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama temel yolu ile geçer.</span><span class="sxs-lookup"><span data-stu-id="993fa-235">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="993fa-236">[Usepathbaseextensions. usepathbase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ara yazılımı, yolu HttpRequest. [Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve uygulama temeli yoluna, [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine böler.</span><span class="sxs-lookup"><span data-stu-id="993fa-236">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="993fa-237">, `/foo` Olarak geçirilen bir ara sunucu yolu için uygulama temel yolu ise `/foo/api/1` , ara yazılım `Request.PathBase` `/foo` `Request.Path` `/api/1` aşağıdaki komutla ve olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="993fa-237">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="993fa-238">Özgün yol ve yol tabanı, ara yazılım ters ' de çağrıldığında yeniden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-238">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="993fa-239">Ara yazılım siparişi işleme hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..</span><span class="sxs-lookup"><span data-stu-id="993fa-239">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="993fa-240">Proxy, yolu kırpar (örneğin, ' `/foo/api/1` ye iletme `/api/1` ), Isteğin [pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yeniden yönlendirmeleri ve bağlantıları düzeltir:</span><span class="sxs-lookup"><span data-stu-id="993fa-240">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="993fa-241">Proxy, yol verileri ekliyor ise, kullanarak yeniden yönlendirmeleri ve bağlantıları onarmak üzere yolun bir bölümünü atın ve özelliğini kullanarak yeniden yönlendirmeyi ve bağlantıları onarın <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="993fa-241">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="993fa-242">Farklı üstbilgi adları kullanan bir ara sunucu için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="993fa-242">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="993fa-243">Proxy, adlı üst bilgileri kullanmıyorsa `X-Forwarded-For` ve `X-Forwarded-Proto` proxy adresini/bağlantı noktasını ve kaynak düzen bilgilerini iletmek için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="993fa-243">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="993fa-244">IPv6 adresi olarak temsil edilen IPv4 adresinin yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993fa-244">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="993fa-245">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 'da veya olarak temsil edilen IPv4 `::ffff:10.0.0.1` `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-245">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="993fa-246">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="993fa-246">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="993fa-247">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-247">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="993fa-248">Aşağıdaki örnekte, iletilen üstbilgiler sağlayan bir ağ adresi `KnownNetworks` listeye IPv6 biçiminde eklenir.</span><span class="sxs-lookup"><span data-stu-id="993fa-248">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="993fa-249">IPv4 adresi:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="993fa-249">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="993fa-250">Dönüştürülen IPv6 adresi:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="993fa-250">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="993fa-251">Dönüştürülen önek uzunluğu: 104</span><span class="sxs-lookup"><span data-stu-id="993fa-251">Converted prefix length: 104</span></span>

<span data-ttu-id="993fa-252">Adresi onaltılık biçimde ( `10.11.12.1` IPv6 olarak temsil edilir) de sağlayabilirsiniz `::ffff:0a0b:0c01` .</span><span class="sxs-lookup"><span data-stu-id="993fa-252">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="993fa-253">Bir IPv4 adresini IPv6 'ya dönüştürürken, `8` ek `::ffff:` IPv6 öneki (8 + 96 = 104) için (ÖRNEKTEKI) CIDR önek uzunluğuna 96 ekleyin.</span><span class="sxs-lookup"><span data-stu-id="993fa-253">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="993fa-254">Linux ve IIS olmayan ters proxy 'ler için Düzen iletme</span><span class="sxs-lookup"><span data-stu-id="993fa-254">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="993fa-255"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Bir azure Linux App Service, Azure Linux sanal MAKINESI (VM) veya IIS 'nin yanı sıra diğer tüm ters proxy 'ler için dağıtıldığında bir siteyi çağıran ve sonsuz döngüye alan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="993fa-255">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="993fa-256">TLS, ters proxy tarafından sonlandırılır ve Kestrel doğru istek düzeninden haberdar değildir.</span><span class="sxs-lookup"><span data-stu-id="993fa-256">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="993fa-257">OAuth ve OıDC yanlış yeniden yönlendirmeler oluşturduğundan bu yapılandırmada de başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="993fa-257">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="993fa-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS 'nin arkasında çalışırken Iletilen üstbilgiler ara yazılımı ekler ve yapılandırır, ancak Linux için eşleşen otomatik yapılandırma (Apache veya NGINX tümleştirmesi) yoktur.</span><span class="sxs-lookup"><span data-stu-id="993fa-258"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="993fa-259">IIS olmayan senaryolarda düzeni proxy 'den iletmek için, Iletilen üstbilgiler ara yazılımını ekleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993fa-259">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="993fa-260">İçinde `Startup.ConfigureServices` , aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="993fa-260">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="certificate-forwarding"></a><span data-ttu-id="993fa-261">Sertifika iletme</span><span class="sxs-lookup"><span data-stu-id="993fa-261">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="993fa-262">Azure</span><span class="sxs-lookup"><span data-stu-id="993fa-262">Azure</span></span>

<span data-ttu-id="993fa-263">Sertifika iletme için Azure App Service yapılandırmak için, bkz. [Azure App Service IÇIN TLS karşılıklı kimlik doğrulamasını yapılandırma](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="993fa-263">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="993fa-264">Aşağıdaki kılavuz ASP.NET Core uygulamasını yapılandırmaya aittir.</span><span class="sxs-lookup"><span data-stu-id="993fa-264">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="993fa-265">İçinde `Startup.Configure` , çağrısının önüne aşağıdaki kodu ekleyin `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="993fa-265">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="993fa-266">Sertifika Iletme ara yazılımını, Azure 'un kullandığı üst bilgi adını belirtecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993fa-266">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="993fa-267">İçinde `Startup.ConfigureServices` , ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="993fa-267">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="993fa-268">Diğer Web proxy 'leri</span><span class="sxs-lookup"><span data-stu-id="993fa-268">Other web proxies</span></span>

<span data-ttu-id="993fa-269">IIS veya Azure App Service uygulama Isteği yönlendirme (ARR) olmayan bir ara sunucu kullanılıyorsa, proxy 'yi bir HTTP üst bilgisinde aldığı sertifikayı iletecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993fa-269">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="993fa-270">İçinde `Startup.Configure` , çağrısının önüne aşağıdaki kodu ekleyin `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="993fa-270">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="993fa-271">Üst bilgi adını belirtmek için sertifika Iletme ara yazılımını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993fa-271">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="993fa-272">İçinde `Startup.ConfigureServices` , ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="993fa-272">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="993fa-273">Ara sunucu Base64 ile kodlanmazsa (NGINX ile aynı durumda olduğu gibi), `HeaderConverter` seçeneğini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-273">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="993fa-274">İçinde aşağıdaki örneği göz önünde bulundurun `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="993fa-274">Consider the following example in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddCertificateForwarding(options =>
{
    options.CertificateHeader = "YOUR_CUSTOM_HEADER_NAME";
    options.HeaderConverter = (headerValue) => 
    {
        var clientCertificate = 
           /* some conversion logic to create an X509Certificate2 */
        return clientCertificate;
    }
});
```

## <a name="troubleshoot"></a><span data-ttu-id="993fa-275">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="993fa-275">Troubleshoot</span></span>

<span data-ttu-id="993fa-276">Üstbilgiler beklendiği gibi iletilemediği zaman [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="993fa-276">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="993fa-277">Günlükler sorunu gidermek için yeterli bilgi sağlamıyorsa, sunucu tarafından alınan istek üstbilgilerini numaralandırın.</span><span class="sxs-lookup"><span data-stu-id="993fa-277">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="993fa-278">Bir uygulama yanıtına istek üst bilgileri yazmak veya üst bilgileri günlüğe kaydetmek için satır içi ara yazılım kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-278">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="993fa-279">Üst bilgileri uygulamanın yanıtına yazmak için, aşağıdaki Terminal satır içi ara yazılımını, içindeki çağrısından hemen sonra yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="993fa-279">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="993fa-280">Yanıt gövdesi yerine günlüklere yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="993fa-280">You can write to logs instead of the response body.</span></span> <span data-ttu-id="993fa-281">Günlüklere yazmak, sitenin hata ayıklanırken normal şekilde çalışmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-281">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="993fa-282">Yanıt gövdesi yerine günlükleri yazmak için:</span><span class="sxs-lookup"><span data-stu-id="993fa-282">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="993fa-283">`ILogger<Startup>` `Startup` [Başlangıçta günlükleri oluşturma](xref:fundamentals/logging/index#create-logs-in-startup)bölümünde açıklandığı gibi sınıfına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="993fa-283">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="993fa-284">İçindeki çağrısından hemen sonra aşağıdaki satır içi ara yazılımı yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="993fa-284">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="993fa-285">İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler öğesine taşınır `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="993fa-285">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="993fa-286">Belirli bir üst bilgide birden çok değer varsa, Iletilen üstbilgiler ara yazılımı sağdan sola doğru sırada üst bilgileri işler.</span><span class="sxs-lookup"><span data-stu-id="993fa-286">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="993fa-287">Varsayılan değer `ForwardLimit` `1` (bir) ise, değeri arttırılmadığı müddetçe yalnızca en sağdaki üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="993fa-287">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="993fa-288">`KnownProxies`İletilen üstbilgiler işlenmeden önce isteğin özgün uzak IP 'si veya listelerindeki bir girdiyle eşleşmesi gerekir `KnownNetworks` .</span><span class="sxs-lookup"><span data-stu-id="993fa-288">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="993fa-289">Bu, güvenilmeyen proxy 'lerden ileticileri kabul etmediği için üst bilgi yanıltmasını kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="993fa-289">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="993fa-290">Bilinmeyen bir proxy algılandığında günlüğe kaydetme, proxy 'nin adresini gösterir:</span><span class="sxs-lookup"><span data-stu-id="993fa-290">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="993fa-291">Yukarıdaki örnekte, alana 10.0.0.100 bir proxy sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="993fa-291">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="993fa-292">Sunucu, güvenilir bir ara sunucu ise, sunucusunun IP adresini `KnownProxies` ' e (veya güvenilen bir ağı öğesine ekleyin) ekleyin `KnownNetworks` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="993fa-292">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="993fa-293">Daha fazla bilgi için [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-293">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="993fa-294">Yalnızca güvenilen proxy 'lerin ve ağların üstbilgileri iletmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="993fa-294">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="993fa-295">Aksi takdirde, [IP sahtekarlığı](https://www.iplocation.net/ip-spoofing) saldırıları mümkündür.</span><span class="sxs-lookup"><span data-stu-id="993fa-295">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="993fa-296">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="993fa-296">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="993fa-297">Microsoft güvenlik danışmanlık CVE-2018-0787: ayrıcalık yükselmesi güvenlik açığı ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="993fa-297">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="993fa-298">ASP.NET Core için Önerilen yapılandırmada, uygulama IIS/ASP. NET Core modülü, NGINX veya Apache kullanılarak barındırılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-298">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="993fa-299">Proxy sunucuları, yük dengeleyiciler ve diğer ağ gereçleri, genellikle istek hakkında uygulamaya ulaşmadan önce bu bilgileri gizlemediğinde:</span><span class="sxs-lookup"><span data-stu-id="993fa-299">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="993fa-300">HTTPS istekleri HTTP üzerinden proxy olduğunda, özgün düzen (HTTPS) kaybolur ve bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="993fa-300">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="993fa-301">Bir uygulama, Internet veya şirket ağında gerçek kaynağını değil, ara sunucudan bir istek aldığından, kaynak istemci IP adresinin de bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="993fa-301">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="993fa-302">Bu bilgiler, istek işlemede, örneğin yeniden yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konum gibi önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-302">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="993fa-303">İletilen üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="993fa-303">Forwarded headers</span></span>

<span data-ttu-id="993fa-304">Kurala göre, proxy 'leri HTTP başlıklarında iletme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="993fa-304">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="993fa-305">Üst bilgi</span><span class="sxs-lookup"><span data-stu-id="993fa-305">Header</span></span> | <span data-ttu-id="993fa-306">Açıklama</span><span class="sxs-lookup"><span data-stu-id="993fa-306">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="993fa-307">X-Iletilmiş-Için</span><span class="sxs-lookup"><span data-stu-id="993fa-307">X-Forwarded-For</span></span> | <span data-ttu-id="993fa-308">, İsteği başlatan istemciyle ilgili bilgileri ve bir proxy zincirinde sonraki proxy 'leri barındırır.</span><span class="sxs-lookup"><span data-stu-id="993fa-308">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="993fa-309">Bu parametre IP adresleri (ve isteğe bağlı olarak bağlantı noktası numaraları) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-309">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="993fa-310">Bir ara sunucu zincirinde, ilk parametre isteğin ilk yaptığı istemciyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="993fa-310">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="993fa-311">Sonraki proxy tanımlayıcıları izler.</span><span class="sxs-lookup"><span data-stu-id="993fa-311">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="993fa-312">Zincirdeki son proxy, Parametreler listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="993fa-312">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="993fa-313">Son proxy 'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanında uzak IP adresi olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-313">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="993fa-314">X-Iletilen-proto</span><span class="sxs-lookup"><span data-stu-id="993fa-314">X-Forwarded-Proto</span></span> | <span data-ttu-id="993fa-315">Kaynak düzenin değeri (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="993fa-315">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="993fa-316">Bu değer, istek birden çok proxy 'ye geçen bir düzen listesi de olabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-316">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="993fa-317">X-Iletilen-konak</span><span class="sxs-lookup"><span data-stu-id="993fa-317">X-Forwarded-Host</span></span> | <span data-ttu-id="993fa-318">Ana bilgisayar üst bilgisi alanının özgün değeri.</span><span class="sxs-lookup"><span data-stu-id="993fa-318">The original value of the Host header field.</span></span> <span data-ttu-id="993fa-319">Genellikle, proxy 'ler ana bilgisayar üst bilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="993fa-319">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="993fa-320">Proxy 'nin konak üstbilgilerini doğrulamadığı veya kısıtlayamayacağı sistemleri etkileyen ayrıcalık yükselmesi güvenlik açığı hakkında bilgi için bkz. [Microsoft Güvenlik Danışmanlığı CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) .</span><span class="sxs-lookup"><span data-stu-id="993fa-320">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="993fa-321">[Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki Iletilen üstbilgiler ara yazılımı, bu üst bilgileri okur ve ilgili alanları üzerinde doldurur <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="993fa-321">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="993fa-322">Ara yazılım güncelleştirmeleri:</span><span class="sxs-lookup"><span data-stu-id="993fa-322">The middleware updates:</span></span>

* <span data-ttu-id="993fa-323">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-323">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="993fa-324">Ek ayarlar, ara yazılımın nasıl ayarlanacağını etkiler `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="993fa-324">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="993fa-325">Ayrıntılar için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="993fa-325">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="993fa-326">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-326">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="993fa-327">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-327">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="993fa-328">İletilen üstbilgiler ara yazılımı [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-328">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="993fa-329">Varsayılan ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="993fa-329">The default settings are:</span></span>

* <span data-ttu-id="993fa-330">Uygulama ve isteklerin kaynağı arasında yalnızca *bir ara sunucu* vardır.</span><span class="sxs-lookup"><span data-stu-id="993fa-330">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="993fa-331">Bilinen proxy 'ler ve bilinen ağlar için yalnızca geri döngü adresleri yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-331">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="993fa-332">İletilen üstbilgiler ve olarak adlandırılır `X-Forwarded-For` `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="993fa-332">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="993fa-333">Tüm ağ gereçleri `X-Forwarded-For` `X-Forwarded-Proto` ek yapılandırma olmadan ve üst bilgilerini eklemez.</span><span class="sxs-lookup"><span data-stu-id="993fa-333">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="993fa-334">Proxy istekler uygulamaya ulaştığında bu üst bilgileri içermiyorsa, Gereç üreticinizin kılavuzuna başvurun.</span><span class="sxs-lookup"><span data-stu-id="993fa-334">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="993fa-335">Gereç, ve dışındaki farklı üstbilgi adlarını kullanıyorsa `X-Forwarded-For` `X-Forwarded-Proto` , <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini gereç tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-335">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="993fa-336">Daha fazla bilgi için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) ve [farklı üstbilgi adları kullanan bir proxy için yapılandırma](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="993fa-336">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="993fa-337">IIS/IIS Express ve ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="993fa-337">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="993fa-338">İletilen üstbilgiler ara yazılımı, uygulama IIS 'nin arkasında ve ASP.NET Core modülünden sonra [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) barındırılıyorsa [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-338">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="993fa-339">İletilen üstbilgiler ara yazılımı, iletilen üst bilgilerle (örneğin, [IP aldatmacası](https://www.iplocation.net/ip-spoofing)) güven sorunları nedeniyle ASP.NET Core modülüne özgü kısıtlanmış bir yapılandırmaya sahip olan ara yazılım ardışık düzeninde ilk olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="993fa-339">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="993fa-340">Ara yazılım, `X-Forwarded-For` ve üst bilgilerini iletmek üzere yapılandırılmıştır `X-Forwarded-Proto` ve tek bir localhost proxy ile kısıtlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-340">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="993fa-341">Ek yapılandırma gerekliyse, [Iletilen üstbilgiler ara yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-341">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="993fa-342">Diğer proxy sunucu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="993fa-342">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="993fa-343">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında [IIS tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 'nin kullanılması dışında, iletilen üstbilgiler ara yazılımı varsayılan olarak etkinleştirilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="993fa-343">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="993fa-344">İletilen üstbilgileri ile işlemek için bir uygulamanın yönlendirilmiş üst bilgi ara yazılımı etkinleştirilmelidir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="993fa-344">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="993fa-345">Ara yazılım için Hayır belirtilmemişse, ara yazılımı etkinleştirdikten sonra <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> varsayılan [Forwardedheadersoptions. Forwardedheaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) , [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dir.</span><span class="sxs-lookup"><span data-stu-id="993fa-345">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="993fa-346">İle ara yazılımı, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` içindeki ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="993fa-346">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="993fa-347"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` Diğer ara yazılım çağrılmadan önce metodunu çağırın:</span><span class="sxs-lookup"><span data-stu-id="993fa-347">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = 
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseExceptionHandler("/Home/Error");
    }

    app.UseStaticFiles();
    // In ASP.NET Core 1.x, replace the following line with: app.UseIdentity();
    app.UseAuthentication();
    app.UseMvc();
}
```

> [!NOTE]
> <span data-ttu-id="993fa-348">Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `Startup.ConfigureServices` veya doğrudan genişletme yöntemine belirtilmemişse <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , iletmek Için varsayılan üstbilgiler [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="993fa-348">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="993fa-349"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Özelliğin, iletmek için üst bilgilerle yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="993fa-349">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="993fa-350">NGINX yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993fa-350">Nginx configuration</span></span>

<span data-ttu-id="993fa-351">`X-Forwarded-For`Ve `X-Forwarded-Proto` üst bilgilerini iletmek için bkz <xref:host-and-deploy/linux-nginx#configure-nginx> ..</span><span class="sxs-lookup"><span data-stu-id="993fa-351">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="993fa-352">Daha fazla bilgi için bkz. [NGINX: iletilen üstbilgiyi kullanma](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="993fa-352">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="993fa-353">Apache yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993fa-353">Apache configuration</span></span>

<span data-ttu-id="993fa-354">`X-Forwarded-For`otomatik olarak eklenir (bkz. [Apache modülü mod_proxy: ters proxy Istek üstbilgileri](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="993fa-354">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="993fa-355">Üstbilgiyi iletme hakkında daha fazla bilgi için `X-Forwarded-Proto` bkz <xref:host-and-deploy/linux-apache#configure-apache> ..</span><span class="sxs-lookup"><span data-stu-id="993fa-355">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="993fa-356">İletilen üstbilgiler ara yazılım seçenekleri</span><span class="sxs-lookup"><span data-stu-id="993fa-356">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="993fa-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen üstbilgiler ara yazılımı davranışını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="993fa-357"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="993fa-358">Aşağıdaki örnek varsayılan değerleri değiştirir:</span><span class="sxs-lookup"><span data-stu-id="993fa-358">The following example changes the default values:</span></span>

* <span data-ttu-id="993fa-359">İletilen üst bilgilerdeki giriş sayısını sınırlayın `2` .</span><span class="sxs-lookup"><span data-stu-id="993fa-359">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="993fa-360">Bilinen bir proxy adresi ekleyin `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="993fa-360">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="993fa-361">İletilen üst bilgi adını varsayılan değerinden olarak değiştirin `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="993fa-361">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="993fa-362">Seçenek</span><span class="sxs-lookup"><span data-stu-id="993fa-362">Option</span></span> | <span data-ttu-id="993fa-363">Açıklama</span><span class="sxs-lookup"><span data-stu-id="993fa-363">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="993fa-364">Ana bilgisayarları üst bilgiyle `X-Forwarded-Host` belirtilen değerlerle sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="993fa-364">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="993fa-365">Değerler, Ordinal-Ignore-Case kullanılarak karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-365">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="993fa-366">Bağlantı noktası numaraları dışlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993fa-366">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="993fa-367">Liste boşsa, tüm konaklara izin verilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-367">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="993fa-368">Üst düzey joker karakter `*` boş olmayan tüm konaklara izin verir.</span><span class="sxs-lookup"><span data-stu-id="993fa-368">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="993fa-369">Alt etki alanı Joker karakterlere izin verilir ancak kök etki alanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="993fa-369">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="993fa-370">Örneğin, `*.contoso.com` `foo.contoso.com` kök etki alanını değil, alt etki alanıyla eşleşir `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="993fa-370">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="993fa-371">Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [puni koduna](https://tools.ietf.org/html/rfc3492) dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="993fa-371">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="993fa-372">[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı ayraçları içermeli ve [geleneksel biçimde](https://tools.ietf.org/html/rfc4291#section-2.2) olmalıdır (örneğin, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-372">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="993fa-373">IPv6 adresleri, farklı biçimler arasında mantıksal eşitlik denetimi için özel değildir ve hiçbir kurallı kullanım yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="993fa-373">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="993fa-374">İzin verilen konaklar kısıtlanamaması, bir saldırganın hizmet tarafından oluşturulan bağlantıları sızdırmasına izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-374">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="993fa-375">Varsayılan değer boş `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="993fa-375">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="993fa-376">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-376">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="993fa-377">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-For` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-377">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="993fa-378">Varsayılan değer: `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="993fa-378">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="993fa-379">Hangi ileticilerin işleneceğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="993fa-379">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="993fa-380">Uygulanan alanların listesi için [Forwardedheaders numaralandırması](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-380">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="993fa-381">Bu özelliğe atanan tipik değerler şunlardır `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="993fa-381">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="993fa-382">Varsayılan değer [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="993fa-382">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="993fa-383">[Forwardedheadersdefaults varsayılan. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-383">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="993fa-384">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Host` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-384">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="993fa-385">Varsayılan değer: `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="993fa-385">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="993fa-386">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-386">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="993fa-387">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Proto` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="993fa-387">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="993fa-388">Varsayılan değer: `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="993fa-388">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="993fa-389">İşlenen üst bilgilerdeki giriş sayısını sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="993fa-389">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="993fa-390">`null`Sınırı devre dışı bırakmak için olarak ayarlayın, ancak bu yalnızca `KnownProxies` veya `KnownNetworks` yapılandırılmışsa yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="993fa-390">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="993fa-391">Değer olmayan bir `null` önlem, hatalı yapılandırılmış proxy 'lerde ve ağdaki yan kanallardan gelen kötü amaçlı isteklere karşı koruma sağlamak için bir önlem (garanti değil) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-391">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="993fa-392">İletilen üstbilgiler ara yazılımı, üst bilgileri sağdan sola doğru sırada işler.</span><span class="sxs-lookup"><span data-stu-id="993fa-392">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="993fa-393">Varsayılan değer ( `1` ) kullanılırsa, değeri artmadığı müddetçe yalnızca üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="993fa-393">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="993fa-394">Varsayılan değer: `1`.</span><span class="sxs-lookup"><span data-stu-id="993fa-394">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="993fa-395">İletilen üstbilgileri kabul etmek için bilinen ağların adres aralıkları.</span><span class="sxs-lookup"><span data-stu-id="993fa-395">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="993fa-396">Sınıfsız Etki alanları arası yönlendirme (CıDR) gösterimini kullanarak IP aralıkları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-396">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="993fa-397">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-397">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="993fa-398">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="993fa-398">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="993fa-399">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-399">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="993fa-400">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-400">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="993fa-401">Varsayılan, `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> için tek bir giriş içeren bir> `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="993fa-401">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="993fa-402">İletilen üstbilgileri kabul etmek için bilinen proxy 'lerin adresleri.</span><span class="sxs-lookup"><span data-stu-id="993fa-402">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="993fa-403">`KnownProxies`Tam IP adresi eşleşmelerini belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-403">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="993fa-404">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-404">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="993fa-405">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="993fa-405">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="993fa-406">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-406">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="993fa-407">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-407">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="993fa-408">Varsayılan, `IList` \<<xref:System.Net.IPAddress> için tek bir giriş içeren bir> `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="993fa-408">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="993fa-409">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="993fa-410">Varsayılan değer: `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="993fa-410">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="993fa-411">[Forwardedheadersdefaults varsayılan. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-411">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="993fa-412">Varsayılan değer: `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="993fa-412">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="993fa-413">Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-413">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="993fa-414">Varsayılan değer: `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="993fa-414">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="993fa-415">Bilgi işlem sayısının, işlenmekte olan [Forwardedheadersoptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitlenmiş olmasını gerektir.</span><span class="sxs-lookup"><span data-stu-id="993fa-415">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="993fa-416">ASP.NET Core 1. x içindeki varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="993fa-416">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="993fa-417">ASP.NET Core 2,0 veya sonraki sürümlerde varsayılan değer `false` .</span><span class="sxs-lookup"><span data-stu-id="993fa-417">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="993fa-418">Senaryolar ve kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="993fa-418">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="993fa-419">İletilen üstbilgiler eklemek mümkün olmadığında ve tüm istekler güvenlidir</span><span class="sxs-lookup"><span data-stu-id="993fa-419">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="993fa-420">Bazı durumlarda, iletilen üstbilgileri uygulamaya yönelik isteklere eklemek mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-420">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="993fa-421">Proxy tüm genel dış isteklerin HTTPS olduğunu zortacaktır, Düzen `Startup.Configure` herhangi bir tür ara yazılım kullanılmadan önce içinde el ile ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="993fa-421">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="993fa-422">Bu kod bir geliştirme veya hazırlama ortamındaki ortam değişkeniyle veya diğer yapılandırma ayarıyla devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="993fa-422">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="993fa-423">Yol tabanı ve istek yolunu değiştiren proxy 'ler ile uğraşın</span><span class="sxs-lookup"><span data-stu-id="993fa-423">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="993fa-424">Bazı proxy 'ler yolu bozulmadan, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama temel yolu ile geçer.</span><span class="sxs-lookup"><span data-stu-id="993fa-424">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="993fa-425">[Usepathbaseextensions. usepathbase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ara yazılımı, yolu HttpRequest. [Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve uygulama temeli yoluna, [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine böler.</span><span class="sxs-lookup"><span data-stu-id="993fa-425">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="993fa-426">, `/foo` Olarak geçirilen bir ara sunucu yolu için uygulama temel yolu ise `/foo/api/1` , ara yazılım `Request.PathBase` `/foo` `Request.Path` `/api/1` aşağıdaki komutla ve olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="993fa-426">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="993fa-427">Özgün yol ve yol tabanı, ara yazılım ters ' de çağrıldığında yeniden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-427">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="993fa-428">Ara yazılım siparişi işleme hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..</span><span class="sxs-lookup"><span data-stu-id="993fa-428">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="993fa-429">Proxy, yolu kırpar (örneğin, ' `/foo/api/1` ye iletme `/api/1` ), Isteğin [pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yeniden yönlendirmeleri ve bağlantıları düzeltir:</span><span class="sxs-lookup"><span data-stu-id="993fa-429">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="993fa-430">Proxy, yol verileri ekliyor ise, kullanarak yeniden yönlendirmeleri ve bağlantıları onarmak üzere yolun bir bölümünü atın ve özelliğini kullanarak yeniden yönlendirmeyi ve bağlantıları onarın <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="993fa-430">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

```csharp
app.Use((context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/foo", out var remainder))
    {
        context.Request.Path = remainder;
    }

    return next();
});
```

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="993fa-431">Farklı üstbilgi adları kullanan bir ara sunucu için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="993fa-431">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="993fa-432">Proxy, adlı üst bilgileri kullanmıyorsa `X-Forwarded-For` ve `X-Forwarded-Proto` proxy adresini/bağlantı noktasını ve kaynak düzen bilgilerini iletmek için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="993fa-432">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="993fa-433">IPv6 adresi olarak temsil edilen IPv4 adresinin yapılandırması</span><span class="sxs-lookup"><span data-stu-id="993fa-433">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="993fa-434">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 'da veya olarak temsil edilen IPv4 `::ffff:10.0.0.1` `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="993fa-434">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="993fa-435">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="993fa-435">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="993fa-436">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="993fa-436">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="993fa-437">Aşağıdaki örnekte, iletilen üstbilgiler sağlayan bir ağ adresi `KnownNetworks` listeye IPv6 biçiminde eklenir.</span><span class="sxs-lookup"><span data-stu-id="993fa-437">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="993fa-438">IPv4 adresi:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="993fa-438">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="993fa-439">Dönüştürülen IPv6 adresi:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="993fa-439">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="993fa-440">Dönüştürülen önek uzunluğu: 104</span><span class="sxs-lookup"><span data-stu-id="993fa-440">Converted prefix length: 104</span></span>

<span data-ttu-id="993fa-441">Adresi onaltılık biçimde ( `10.11.12.1` IPv6 olarak temsil edilir) de sağlayabilirsiniz `::ffff:0a0b:0c01` .</span><span class="sxs-lookup"><span data-stu-id="993fa-441">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="993fa-442">Bir IPv4 adresini IPv6 'ya dönüştürürken, `8` ek `::ffff:` IPv6 öneki (8 + 96 = 104) için (ÖRNEKTEKI) CIDR önek uzunluğuna 96 ekleyin.</span><span class="sxs-lookup"><span data-stu-id="993fa-442">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

```csharp
// To access IPNetwork and IPAddress, add the following namespaces:
// using using System.Net;
// using Microsoft.AspNetCore.HttpOverrides;
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedHeaders =
        ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
    options.KnownNetworks.Add(new IPNetwork(
        IPAddress.Parse("::ffff:10.11.12.1"), 104));
});
```

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="993fa-443">Linux ve IIS olmayan ters proxy 'ler için Düzen iletme</span><span class="sxs-lookup"><span data-stu-id="993fa-443">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="993fa-444"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Bir azure Linux App Service, Azure Linux sanal MAKINESI (VM) veya IIS 'nin yanı sıra diğer tüm ters proxy 'ler için dağıtıldığında bir siteyi çağıran ve sonsuz döngüye alan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="993fa-444">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="993fa-445">TLS, ters proxy tarafından sonlandırılır ve Kestrel doğru istek düzeninden haberdar değildir.</span><span class="sxs-lookup"><span data-stu-id="993fa-445">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="993fa-446">OAuth ve OıDC yanlış yeniden yönlendirmeler oluşturduğundan bu yapılandırmada de başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="993fa-446">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="993fa-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS 'nin arkasında çalışırken Iletilen üstbilgiler ara yazılımı ekler ve yapılandırır, ancak Linux için eşleşen otomatik yapılandırma (Apache veya NGINX tümleştirmesi) yoktur.</span><span class="sxs-lookup"><span data-stu-id="993fa-447"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="993fa-448">IIS olmayan senaryolarda düzeni proxy 'den iletmek için, Iletilen üstbilgiler ara yazılımını ekleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="993fa-448">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="993fa-449">İçinde `Startup.ConfigureServices` , aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="993fa-449">In `Startup.ConfigureServices`, use the following code:</span></span>

```csharp
// using Microsoft.AspNetCore.HttpOverrides;

if (string.Equals(
    Environment.GetEnvironmentVariable("ASPNETCORE_FORWARDEDHEADERS_ENABLED"), 
    "true", StringComparison.OrdinalIgnoreCase))
{
    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders = ForwardedHeaders.XForwardedFor | 
            ForwardedHeaders.XForwardedProto;
        // Only loopback proxies are allowed by default.
        // Clear that restriction because forwarders are enabled by explicit 
        // configuration.
        options.KnownNetworks.Clear();
        options.KnownProxies.Clear();
    });
}
```

## <a name="troubleshoot"></a><span data-ttu-id="993fa-450">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="993fa-450">Troubleshoot</span></span>

<span data-ttu-id="993fa-451">Üstbilgiler beklendiği gibi iletilemediği zaman [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="993fa-451">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="993fa-452">Günlükler sorunu gidermek için yeterli bilgi sağlamıyorsa, sunucu tarafından alınan istek üstbilgilerini numaralandırın.</span><span class="sxs-lookup"><span data-stu-id="993fa-452">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="993fa-453">Bir uygulama yanıtına istek üst bilgileri yazmak veya üst bilgileri günlüğe kaydetmek için satır içi ara yazılım kullanın.</span><span class="sxs-lookup"><span data-stu-id="993fa-453">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="993fa-454">Üst bilgileri uygulamanın yanıtına yazmak için, aşağıdaki Terminal satır içi ara yazılımını, içindeki çağrısından hemen sonra yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="993fa-454">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

```csharp
app.Run(async (context) =>
{
    context.Response.ContentType = "text/plain";

    // Request method, scheme, and path
    await context.Response.WriteAsync(
        $"Request Method: {context.Request.Method}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Scheme: {context.Request.Scheme}{Environment.NewLine}");
    await context.Response.WriteAsync(
        $"Request Path: {context.Request.Path}{Environment.NewLine}");

    // Headers
    await context.Response.WriteAsync($"Request Headers:{Environment.NewLine}");

    foreach (var header in context.Request.Headers)
    {
        await context.Response.WriteAsync($"{header.Key}: " +
            $"{header.Value}{Environment.NewLine}");
    }

    await context.Response.WriteAsync(Environment.NewLine);

    // Connection: RemoteIp
    await context.Response.WriteAsync(
        $"Request RemoteIp: {context.Connection.RemoteIpAddress}");
});
```

<span data-ttu-id="993fa-455">Yanıt gövdesi yerine günlüklere yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="993fa-455">You can write to logs instead of the response body.</span></span> <span data-ttu-id="993fa-456">Günlüklere yazmak, sitenin hata ayıklanırken normal şekilde çalışmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="993fa-456">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="993fa-457">Yanıt gövdesi yerine günlükleri yazmak için:</span><span class="sxs-lookup"><span data-stu-id="993fa-457">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="993fa-458">`ILogger<Startup>` `Startup` [Başlangıçta günlükleri oluşturma](xref:fundamentals/logging/index#create-logs-in-startup)bölümünde açıklandığı gibi sınıfına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="993fa-458">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="993fa-459">İçindeki çağrısından hemen sonra aşağıdaki satır içi ara yazılımı yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="993fa-459">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

```csharp
app.Use(async (context, next) =>
{
    // Request method, scheme, and path
    _logger.LogDebug("Request Method: {Method}", context.Request.Method);
    _logger.LogDebug("Request Scheme: {Scheme}", context.Request.Scheme);
    _logger.LogDebug("Request Path: {Path}", context.Request.Path);

    // Headers
    foreach (var header in context.Request.Headers)
    {
        _logger.LogDebug("Header: {Key}: {Value}", header.Key, header.Value);
    }

    // Connection: RemoteIp
    _logger.LogDebug("Request RemoteIp: {RemoteIpAddress}", 
        context.Connection.RemoteIpAddress);

    await next();
});
```

<span data-ttu-id="993fa-460">İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler öğesine taşınır `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="993fa-460">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="993fa-461">Belirli bir üst bilgide birden çok değer varsa, Iletilen üstbilgiler ara yazılımı sağdan sola doğru sırada üst bilgileri işler.</span><span class="sxs-lookup"><span data-stu-id="993fa-461">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="993fa-462">Varsayılan değer `ForwardLimit` `1` (bir) ise, değeri arttırılmadığı müddetçe yalnızca en sağdaki üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="993fa-462">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="993fa-463">`KnownProxies`İletilen üstbilgiler işlenmeden önce isteğin özgün uzak IP 'si veya listelerindeki bir girdiyle eşleşmesi gerekir `KnownNetworks` .</span><span class="sxs-lookup"><span data-stu-id="993fa-463">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="993fa-464">Bu, güvenilmeyen proxy 'lerden ileticileri kabul etmediği için üst bilgi yanıltmasını kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="993fa-464">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="993fa-465">Bilinmeyen bir proxy algılandığında günlüğe kaydetme, proxy 'nin adresini gösterir:</span><span class="sxs-lookup"><span data-stu-id="993fa-465">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="993fa-466">Yukarıdaki örnekte, alana 10.0.0.100 bir proxy sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="993fa-466">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="993fa-467">Sunucu, güvenilir bir ara sunucu ise, sunucusunun IP adresini `KnownProxies` ' e (veya güvenilen bir ağı öğesine ekleyin) ekleyin `KnownNetworks` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="993fa-467">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="993fa-468">Daha fazla bilgi için [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="993fa-468">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="993fa-469">Yalnızca güvenilen proxy 'lerin ve ağların üstbilgileri iletmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="993fa-469">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="993fa-470">Aksi takdirde, [IP sahtekarlığı](https://www.iplocation.net/ip-spoofing) saldırıları mümkündür.</span><span class="sxs-lookup"><span data-stu-id="993fa-470">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="993fa-471">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="993fa-471">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="993fa-472">Microsoft güvenlik danışmanlık CVE-2018-0787: ayrıcalık yükselmesi güvenlik açığı ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="993fa-472">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
