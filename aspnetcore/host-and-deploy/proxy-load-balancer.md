---
title: Proxy sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core yapılandırma
author: rick-anderson
description: Proxy sunucuları ve yük dengeleyicileri arkasında barındırılan ve genellikle önemli istek bilgilerini gizleyen uygulamalar için yapılandırma hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: host-and-deploy/proxy-load-balancer
ms.openlocfilehash: b5c81e0cfa29cddeb1aeed1119a711fca4d91ae4
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78659385"
---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="773c5-103">Proxy sunucuları ve yük dengeleyicileri ile çalışmak için ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="773c5-103">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="773c5-104">Yazar: [Chris Ross](https://github.com/Tratcher)</span><span class="sxs-lookup"><span data-stu-id="773c5-104">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="773c5-105">ASP.NET Core için önerilen yapılandırmada uygulama IIS/ASP.NET Core Module, Nginx veya Apache kullanılarak barındırılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-105">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="773c5-106">Proxy sunucular, yük dengeleyicileri ve diğer ağ cihazları genellikle uygulamaya ulaşmadan önce istek le ilgili bilgileri belirsiz hale getirir:</span><span class="sxs-lookup"><span data-stu-id="773c5-106">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="773c5-107">HTTPS istekleri HTTP üzerinden iliştirildiğinde, özgün düzen (HTTPS) kaybolur ve bir üstbilgide iletilmelidir.</span><span class="sxs-lookup"><span data-stu-id="773c5-107">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="773c5-108">Bir uygulama, Internet veya şirket ağındaki gerçek kaynağından değil, proxy'den bir istek aldığından, kaynak olan istemci IP adreside de üstbilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="773c5-108">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="773c5-109">Bu bilgiler, yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konumlandırma gibi istek işlemede önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-109">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="773c5-110">İleriye dönük üstbilgi</span><span class="sxs-lookup"><span data-stu-id="773c5-110">Forwarded headers</span></span>

<span data-ttu-id="773c5-111">Sözleşmeye göre, vekiller BILGILERI HTTP üstbilgilerine iletir.</span><span class="sxs-lookup"><span data-stu-id="773c5-111">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="773c5-112">Üst bilgi</span><span class="sxs-lookup"><span data-stu-id="773c5-112">Header</span></span> | <span data-ttu-id="773c5-113">Açıklama</span><span class="sxs-lookup"><span data-stu-id="773c5-113">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="773c5-114">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="773c5-114">X-Forwarded-For</span></span> | <span data-ttu-id="773c5-115">İsteği başlatan istemci ve sonraki yakınlıklar hakkında bilgileri bir yakınlık lar zincirinde tutar.</span><span class="sxs-lookup"><span data-stu-id="773c5-115">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="773c5-116">Bu parametre IP adresleri (ve isteğe bağlı olarak, bağlantı noktası numaraları) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-116">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="773c5-117">Proxy sunucuları zincirinde, ilk parametre isteğiilk yapıldığı istemciyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="773c5-117">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="773c5-118">Sonraki proxy tanımlayıcıları izleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-118">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="773c5-119">Zincirdeki son proxy parametreler listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="773c5-119">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="773c5-120">Son proxy'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanındaki uzak IP adresi olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-120">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="773c5-121">X-İlli-Proto</span><span class="sxs-lookup"><span data-stu-id="773c5-121">X-Forwarded-Proto</span></span> | <span data-ttu-id="773c5-122">Kaynak düzenin in değeri (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="773c5-122">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="773c5-123">İstek birden çok yakınlık arasında geçtiyse, değer de şemaları listesi olabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-123">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="773c5-124">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="773c5-124">X-Forwarded-Host</span></span> | <span data-ttu-id="773c5-125">Ana Bilgisayar üstbilgi alanının özgün değeri.</span><span class="sxs-lookup"><span data-stu-id="773c5-125">The original value of the Host header field.</span></span> <span data-ttu-id="773c5-126">Genellikle, yakınlık ana bilgisayar üstbilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-126">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="773c5-127">Proxy'nin Ana Bilgisayar üstbilgilerini doğrulayamadığı veya bilinen iyi değerlerle sınırlamadığı sistemleri etkileyen ayrıcalıkların yükseltisi güvenlik açığı hakkında bilgi almak için [Microsoft Güvenlik Danışma Belgesi CVE-2018-0787'ye](https://github.com/aspnet/Announcements/issues/295) bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-127">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="773c5-128">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden Iletilen Üstbilgi Middleware, bu başlıkları okur ve ilgili <xref:Microsoft.AspNetCore.Http.HttpContext>alanları doldurur.</span><span class="sxs-lookup"><span data-stu-id="773c5-128">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="773c5-129">Ara yazılım güncellemeleri:</span><span class="sxs-lookup"><span data-stu-id="773c5-129">The middleware updates:</span></span>

* <span data-ttu-id="773c5-130">Başlık değerini kullanarak `X-Forwarded-For` [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set.</span><span class="sxs-lookup"><span data-stu-id="773c5-130">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="773c5-131">Ek ayarlar, ara yazılımın nasıl ayarlanır' ı `RemoteIpAddress`etkiler.</span><span class="sxs-lookup"><span data-stu-id="773c5-131">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="773c5-132">Ayrıntılar [için, İlezli Üstbilgiler Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-132">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="773c5-133">Başlık değerini kullanarak `X-Forwarded-Proto` [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set.</span><span class="sxs-lookup"><span data-stu-id="773c5-133">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="773c5-134">Başlık değerini kullanarak `X-Forwarded-Host` [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set.</span><span class="sxs-lookup"><span data-stu-id="773c5-134">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="773c5-135">İlezli Üstbilgi Middleware [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-135">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="773c5-136">Varsayılan ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="773c5-136">The default settings are:</span></span>

* <span data-ttu-id="773c5-137">Uygulama ve isteklerin kaynağı arasında yalnızca *bir proxy* vardır.</span><span class="sxs-lookup"><span data-stu-id="773c5-137">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="773c5-138">Yalnızca geri dönüş adresleri bilinen yakınlıklar ve bilinen ağlar için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-138">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="773c5-139">İlerlenen üstbilgi `X-Forwarded-For` adlandırılmış ve `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="773c5-139">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="773c5-140">Tüm ağ cihazları `X-Forwarded-For` ek `X-Forwarded-Proto` yapılandırma olmadan üstbilgi ve üstbilgi eklemez.</span><span class="sxs-lookup"><span data-stu-id="773c5-140">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="773c5-141">Yakın istekleri uygulamaya ulaştıklarında bu üstbilgileri içermiyorsa, cihaz üreticinizin kılavuzuna başvurun.</span><span class="sxs-lookup"><span data-stu-id="773c5-141">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="773c5-142">Cihaz, cihazdan `X-Forwarded-For` farklı üstbilgi `X-Forwarded-Proto`adları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> kullanıyorsa ve cihaz tarafından kullanılan üstbilgi adlarıyla eşleşecek <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> şekilde ve seçenekleri ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="773c5-142">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="773c5-143">Daha fazla bilgi için, [farklı üstbilgi adları kullanan bir proxy için](#configuration-for-a-proxy-that-uses-different-header-names) [Iletili Üstbilgiler Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) ve Yapılandırma'ya bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-143">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="773c5-144">IIS/IIS Express ve ASP.NET Çekirdek Modülü</span><span class="sxs-lookup"><span data-stu-id="773c5-144">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="773c5-145">İleşli Üstbilgi Araware, uygulama IIS ve ASP.NET Çekirdek Modülü' nün arkasında [sürücü](xref:host-and-deploy/iis/index#out-of-process-hosting-model) lük sınır dışında barındırılırken Varsayılan olarak [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-145">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="773c5-146">İleli Üstbilgi Middleware, iletili üstdeğerlere [(örneğin, IP sızdırma)](https://www.iplocation.net/ip-spoofing)güven endişeleri nedeniyle ASP.NET Çekirdek Modülüne özgü sınırlı bir yapılandırmaile ilk olarak ara yazılım boru hattında çalışacak şekilde etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-146">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="773c5-147">Ara yazılım, üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi ileri ye doğru yapılandırılır ve tek bir localhost proxy ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="773c5-147">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="773c5-148">Ek yapılandırma gerekiyorsa, [İleriye Dönük Üstbilgi Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-148">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="773c5-149">Diğer proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="773c5-149">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="773c5-150">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma yaparken [IIS Tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) kullanmanın dışında, Iletili Üstbilgi Middleware varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-150">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="773c5-151">İleşli Üstbilgi Ara ware, bir uygulamanın <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>iletilir üstbilgisini ' ile işlemesi için etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="773c5-151">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="773c5-152">Middleware'e hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse ara yazılımı etkinleştirdikten sonra, varsayılan [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) [forwardedheaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="773c5-152">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="773c5-153">Aracı, 'deki <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` üstbilgi ve `X-Forwarded-Proto` üstileri `Startup.ConfigureServices`iletmek için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-153">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="773c5-154">Diğer <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> ara `Startup.Configure` yazılımları çağırmadan önce yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="773c5-154">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="773c5-155">Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> veya doğrudan `Startup.ConfigureServices` uzantısı yöntemi nde <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>belirtilmişise , ileri varsayılan üstbilgi [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)vardır.</span><span class="sxs-lookup"><span data-stu-id="773c5-155">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="773c5-156">Özellik <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> ileri ye doğru üstbilgi ile yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="773c5-156">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="773c5-157">Nginx yapılandırması</span><span class="sxs-lookup"><span data-stu-id="773c5-157">Nginx configuration</span></span>

<span data-ttu-id="773c5-158">Üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi <xref:host-and-deploy/linux-nginx#configure-nginx>ileri almak için bkz.</span><span class="sxs-lookup"><span data-stu-id="773c5-158">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="773c5-159">Daha fazla bilgi için [NGINX: İleride üstbilgiyi kullanma.](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)</span><span class="sxs-lookup"><span data-stu-id="773c5-159">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="773c5-160">Apache yapılandırması</span><span class="sxs-lookup"><span data-stu-id="773c5-160">Apache configuration</span></span>

<span data-ttu-id="773c5-161">`X-Forwarded-For`otomatik olarak eklenir [(bkz. Apache Modülü mod_proxy: Proxy İstek Üstbilgilerini Ters](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)Çevir ).</span><span class="sxs-lookup"><span data-stu-id="773c5-161">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="773c5-162">Üstbilginin nasıl iletilen `X-Forwarded-Proto` <xref:host-and-deploy/linux-apache#configure-apache>hakkında bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="773c5-162">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="773c5-163">İleşli Üstbilgi Ara ware seçenekleri</span><span class="sxs-lookup"><span data-stu-id="773c5-163">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="773c5-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen Üstbilgi Middleware davranışını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="773c5-164"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="773c5-165">Aşağıdaki örnekte varsayılan değerleri değiştirir:</span><span class="sxs-lookup"><span data-stu-id="773c5-165">The following example changes the default values:</span></span>

* <span data-ttu-id="773c5-166">İlerlenen üstbilgideki giriş sayısını `2`' la sınırlandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-166">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="773c5-167">`127.0.10.1`Bilinen bir proxy adresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-167">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="773c5-168">İdeyiş üstbilgi adını `X-Forwarded-For` varsayılandan ' a `X-Forwarded-For-My-Custom-Header-Name`çevirin.</span><span class="sxs-lookup"><span data-stu-id="773c5-168">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="773c5-169">Seçenek</span><span class="sxs-lookup"><span data-stu-id="773c5-169">Option</span></span> | <span data-ttu-id="773c5-170">Açıklama</span><span class="sxs-lookup"><span data-stu-id="773c5-170">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="773c5-171">Ana bilgisayarları `X-Forwarded-Host` üstbilgi yle sağlanan değerlerle sınırlandırıyor.</span><span class="sxs-lookup"><span data-stu-id="773c5-171">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="773c5-172">Değerler, ordinal-ignore-case kullanılarak karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-172">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="773c5-173">Bağlantı noktası numaraları hariç tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="773c5-173">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="773c5-174">Liste boşsa, tüm ana bilgisayarlara izin verilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-174">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="773c5-175">Üst düzey joker `*` karakter, boş olmayan tüm ana bilgisayarları sağlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-175">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="773c5-176">Alt etki alanı joker kartlarına izin verilir, ancak kök etki alanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-176">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="773c5-177">Örneğin, `*.contoso.com` alt etki `foo.contoso.com` alanı eşleşir, `contoso.com`ancak kök etki alanı .</span><span class="sxs-lookup"><span data-stu-id="773c5-177">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="773c5-178">Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [Punycode'a](https://tools.ietf.org/html/rfc3492) dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="773c5-178">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="773c5-179">[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı parantezler içermelidir ve [geleneksel](https://tools.ietf.org/html/rfc4291#section-2.2) formda `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`olmalıdır (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="773c5-179">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="773c5-180">IPv6 adresleri, farklı biçimler arasındaki mantıksal eşitliği denetlemek için özel olarak belirtilmemiştir ve hiçbir kanonializasyon gerçekleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-180">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="773c5-181">İzin verilen ana bilgisayarların kısıtlanmasına izin verilmese, saldırgan hizmet tarafından oluşturulan bağlantıları sızdırmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-181">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="773c5-182">Varsayılan değer boştur. `IList<string>`</span><span class="sxs-lookup"><span data-stu-id="773c5-182">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="773c5-183">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-183">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="773c5-184">Bu seçenek, proxy/forwarder `X-Forwarded-For` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-184">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="773c5-185">Varsayılan değer: `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="773c5-185">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="773c5-186">Hangi iletmelerin işlenmesi gerektiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="773c5-186">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="773c5-187">Geçerli alanların listesi için [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-187">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="773c5-188">Bu özelliğe atanan `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tipik değerler.</span><span class="sxs-lookup"><span data-stu-id="773c5-188">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="773c5-189">Varsayılan değer [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)olduğunu.</span><span class="sxs-lookup"><span data-stu-id="773c5-189">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="773c5-190">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-190">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="773c5-191">Bu seçenek, proxy/forwarder `X-Forwarded-Host` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-191">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="773c5-192">Varsayılan değer: `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="773c5-192">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="773c5-193">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-193">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="773c5-194">Bu seçenek, proxy/forwarder `X-Forwarded-Proto` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-194">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="773c5-195">Varsayılan değer: `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="773c5-195">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="773c5-196">İşlenen üstbilgideki giriş sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-196">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="773c5-197">Sınırı devre dışı bırakacak şekilde ayarlayın, ancak `KnownProxies` bu `KnownNetworks` yalnızca yapılandırıldıysa veya yapılandırılırsa yapılmalıdır. `null`</span><span class="sxs-lookup"><span data-stu-id="773c5-197">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="773c5-198">`null` Değer olmayan bir ayar, ağdaki yan kanallardan gelen yanlış yapılandırılmış yardımcılara ve kötü amaçlı isteklere karşı korunmak için bir önlemdir (ancak garanti değildir).</span><span class="sxs-lookup"><span data-stu-id="773c5-198">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="773c5-199">İlli Üstbilgi Middleware, üstleri sağdan sola doğru ters sırada işler.</span><span class="sxs-lookup"><span data-stu-id="773c5-199">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="773c5-200">Varsayılan değer (`1`) kullanılırsa, değeri `ForwardLimit` artırılmadığı sürece yalnızca üstbilgiden en doğru değer işlenir.</span><span class="sxs-lookup"><span data-stu-id="773c5-200">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="773c5-201">Varsayılan değer: `1`.</span><span class="sxs-lookup"><span data-stu-id="773c5-201">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="773c5-202">İleriye dönük üstbilgileri kabul etmek için bilinen ağların adres aralıkları.</span><span class="sxs-lookup"><span data-stu-id="773c5-202">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="773c5-203">Sınıfsız Etki Alanı Yönlendirme (CIDR) gösterimini kullanarak IP aralıkları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="773c5-203">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="773c5-204">Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-204">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="773c5-205">Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="773c5-205">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="773c5-206">[HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-206">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="773c5-207">Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-207">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="773c5-208">Varsayılan değer, `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> '' için `IPAddress.Loopback`tek bir giriş içeren bir>.</span><span class="sxs-lookup"><span data-stu-id="773c5-208">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="773c5-209">İleriye dönük üstbilgi kabul etmek için bilinen yakınlık adresleri.</span><span class="sxs-lookup"><span data-stu-id="773c5-209">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="773c5-210">Tam `KnownProxies` IP adresi eşleşmelerini belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-210">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="773c5-211">Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-211">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="773c5-212">Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="773c5-212">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="773c5-213">[HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-213">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="773c5-214">Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-214">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="773c5-215">Varsayılan değer, `IList` \< <xref:System.Net.IPAddress> '' için `IPAddress.IPv6Loopback`tek bir giriş içeren bir>.</span><span class="sxs-lookup"><span data-stu-id="773c5-215">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="773c5-216">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-216">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="773c5-217">Varsayılan değer: `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="773c5-217">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="773c5-218">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-218">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="773c5-219">Varsayılan değer: `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="773c5-219">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="773c5-220">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-220">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="773c5-221">Varsayılan değer: `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="773c5-221">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="773c5-222">Üstbilgi değerlerinin sayısının, işlenen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitolmasını zorunlu kılmasını.</span><span class="sxs-lookup"><span data-stu-id="773c5-222">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="773c5-223">Core 1.x ASP.NET varsayılan `true`.</span><span class="sxs-lookup"><span data-stu-id="773c5-223">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="773c5-224">Core 2.0 veya sonraki ASP.NET `false`varsayılan.</span><span class="sxs-lookup"><span data-stu-id="773c5-224">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="773c5-225">Senaryolar ve kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="773c5-225">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="773c5-226">İleiletiüstleri eklemek mümkün olmadığında ve tüm isteklerin güvenli olduğu</span><span class="sxs-lookup"><span data-stu-id="773c5-226">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="773c5-227">Bazı durumlarda, uygulamaya iledilen üstbilgi eklemek mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-227">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="773c5-228">Proxy tüm genel dış isteklerin HTTPS olduğunu zorluyorsa, şema `Startup.Configure` herhangi bir ara yazılım türü kullanmadan önce el ile ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="773c5-228">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="773c5-229">Bu kod, bir geliştirme veya evreleme ortamında bir ortam değişkeni veya başka bir yapılandırma ayarı ile devre dışı alınabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-229">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="773c5-230">İstek yolunu değiştiren yol tabanı ve yakınlıklarla başa çık</span><span class="sxs-lookup"><span data-stu-id="773c5-230">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="773c5-231">Bazı yakınlıklar yolu sağlam bir şekilde geçer, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama tabanı yolu ile.</span><span class="sxs-lookup"><span data-stu-id="773c5-231">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="773c5-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware [httprequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine uygulama temel yolu içine yol böler.</span><span class="sxs-lookup"><span data-stu-id="773c5-232">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="773c5-233">Proxy `/foo` `/foo/api/1`yolu olarak geçirilen bir proxy yolu için uygulama `Request.PathBase` `/foo` temel `Request.Path` `/api/1` yolu ise, ara yazılım aşağıdaki komutla ve ayarlar:</span><span class="sxs-lookup"><span data-stu-id="773c5-233">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="773c5-234">Orta yazılım ters olarak yeniden çağrıldığında özgün yol ve yol tabanı yeniden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="773c5-234">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="773c5-235">Ara yazılım siparişi işleme hakkında <xref:fundamentals/middleware/index>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="773c5-235">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="773c5-236">Proxy yolu kırparsa (örneğin, `/foo/api/1` `/api/1`iletilmesi), isteğin [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yönlendirmeleri ve bağlantıları düzeltin:</span><span class="sxs-lookup"><span data-stu-id="773c5-236">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="773c5-237">Proxy yol verileri ekliyorsa, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> özelliği kullanarak ve atayarak yeniden yönlendirmeleri ve bağlantıları düzeltmek için yolun bir kısmını atın:</span><span class="sxs-lookup"><span data-stu-id="773c5-237">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="773c5-238">Farklı üstbilgi adları kullanan bir proxy için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="773c5-238">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="773c5-239">Proxy `X-Forwarded-For` adlandırılmış üstbilgileri kullanmıyorsa `X-Forwarded-Proto` ve proxy adresi/bağlantı noktası ve kaynak <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> düzeni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> bilgilerini iletmek için, proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ve seçenekleri ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="773c5-239">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="773c5-240">IPv6 adresi olarak temsil edilen bir IPv4 adresi için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="773c5-240">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="773c5-241">Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1` IPv6 olarak veya `::ffff:a00:1`olarak temsil edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-241">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="773c5-242">Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="773c5-242">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="773c5-243">[HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-243">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="773c5-244">Aşağıdaki örnekte, iletilen üstbilgileri sağlayan bir ağ `KnownNetworks` adresi IPv6 biçiminde listeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="773c5-244">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="773c5-245">IPv4 adresi:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="773c5-245">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="773c5-246">Dönüştürülmüş IPv6 adresi:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="773c5-246">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="773c5-247">Dönüştürülmüş önek uzunluğu: 104</span><span class="sxs-lookup"><span data-stu-id="773c5-247">Converted prefix length: 104</span></span>

<span data-ttu-id="773c5-248">Adresi hexadecimal formatta da sağlayabilirsiniz (IPv6'da`10.11.12.1` temsil `::ffff:0a0b:0c01`edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-248">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="773c5-249">Bir IPv4 adresini IPv6'ya dönüştürürken, ek`8` `::ffff:` IPv6 önekini (8 + 96 = 104) hesaba katmak için CIDR Önek Uzunluğu'na (örnekte) 96 ekleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-249">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="773c5-250">Linux ve IIS olmayan ters vekiller için şemayı iletin</span><span class="sxs-lookup"><span data-stu-id="773c5-250">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="773c5-251">Bir Azure <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Linux Uygulama Hizmetine, Azure Linux sanal makinesine (VM) veya IIS dışındaki diğer ters proxy'nin arkasına dağıtılırsa, bir siteyi arayan ve sonsuz bir döngüye sokan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="773c5-251">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="773c5-252">TLS ters proxy tarafından sonlandırılır ve Kestrel doğru istek şeması ndan haberdar yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="773c5-252">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="773c5-253">OAuth ve OIDC de yanlış yönlendirmeler oluşturdukları için bu yapılandırmada başarısız olurlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-253">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="773c5-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS'nin arkasında çalışırken İleriye Dönük Üstbilgi Middleware ekler ve yapılandırır, ancak Linux için eşleşen bir otomatik yapılandırma yoktur (Apache veya Nginx entegrasyonu).</span><span class="sxs-lookup"><span data-stu-id="773c5-254"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="773c5-255">Düzeni IIS olmayan senaryolarda proxy'den iletmek için, İleriye Dönük Üstbilgi Ara Ware ekleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-255">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="773c5-256">In `Startup.ConfigureServices`, aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="773c5-256">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="773c5-257">Sertifika iletme</span><span class="sxs-lookup"><span data-stu-id="773c5-257">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="773c5-258">Azure</span><span class="sxs-lookup"><span data-stu-id="773c5-258">Azure</span></span>

<span data-ttu-id="773c5-259">Sertifika iletme için Azure Uygulama Hizmetini yapılandırmak [için](/azure/app-service/app-service-web-configure-tls-mutual-auth)bkz.</span><span class="sxs-lookup"><span data-stu-id="773c5-259">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="773c5-260">Aşağıdaki kılavuz, ASP.NET Core uygulamasının yapılandırılmasıyla ilgilidir.</span><span class="sxs-lookup"><span data-stu-id="773c5-260">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="773c5-261">In `Startup.Configure`, aramadan önce aşağıdaki `app.UseAuthentication();`kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="773c5-261">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="773c5-262">Azure'un kullandığı üstbilgi adını belirtmek için Sertifika Iletme Ara ware'i yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-262">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="773c5-263">In `Startup.ConfigureServices`, ara yazılımın bir sertifika oluşturduğu üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="773c5-263">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="773c5-264">Diğer web yakınlıkları</span><span class="sxs-lookup"><span data-stu-id="773c5-264">Other web proxies</span></span>

<span data-ttu-id="773c5-265">IIS veya Azure Uygulama Hizmetinin Uygulama İsteği Yönlendirmesi (ARR) olmayan bir proxy kullanılıyorsa, proxy'yi http üstbilgisinde aldığı sertifikayı iletmek için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-265">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="773c5-266">In `Startup.Configure`, aramadan önce aşağıdaki `app.UseAuthentication();`kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="773c5-266">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="773c5-267">Üstbilgi adını belirtmek için Sertifika Iletme Aracı'nı yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-267">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="773c5-268">In `Startup.ConfigureServices`, ara yazılımın bir sertifika oluşturduğu üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="773c5-268">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="773c5-269">Proxy base64-kodlama sertifikası (Nginx ile olduğu gibi) değilse, `HeaderConverter` seçeneği ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="773c5-269">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="773c5-270">Aşağıdaki örneği göz `Startup.ConfigureServices`önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="773c5-270">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="773c5-271">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="773c5-271">Troubleshoot</span></span>

<span data-ttu-id="773c5-272">Üstbilgi beklendiği gibi iletilmediğinde, [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="773c5-272">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="773c5-273">Günlükler sorunu gidermek için yeterli bilgi sağlamazsa, sunucu tarafından alınan istek üstbilgilerini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="773c5-273">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="773c5-274">İstek üstbilgilerini bir uygulama yanıtına yazmak veya üstbilgigünlerini günlüğe kaydetmek için satır içi ara yazılım kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-274">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="773c5-275">Üstbilgileri uygulamanın yanıtına yazmak için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> aşağıdaki terminal satır ara sını aşağıdaki `Startup.Configure`çağrıdan hemen sonra yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="773c5-275">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="773c5-276">Yanıt gövdesi yerine günlüklere yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="773c5-276">You can write to logs instead of the response body.</span></span> <span data-ttu-id="773c5-277">Günlüklere yazma, hata ayıklama sırasında sitenin normal çalışmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-277">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="773c5-278">Yanıt gövdesiyerine günlükyazmak için:</span><span class="sxs-lookup"><span data-stu-id="773c5-278">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="773c5-279">Başlangıç'ta `Startup` create [günlüklerinde](xref:fundamentals/logging/index#create-logs-in-startup)açıklandığı gibi sınıfa enjekte edin. `ILogger<Startup>`</span><span class="sxs-lookup"><span data-stu-id="773c5-279">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="773c5-280">Aşağıdaki satır lı ara yazılımı aramadan <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`hemen sonra yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="773c5-280">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="773c5-281">İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler ' `X-Original-{For|Proto|Host}`e taşınır.</span><span class="sxs-lookup"><span data-stu-id="773c5-281">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="773c5-282">Belirli bir üstbilgide birden çok değer varsa, İlliyeli Üstbilgi Orta Ware üstileri sağdan sola doğru ters sırada işler.</span><span class="sxs-lookup"><span data-stu-id="773c5-282">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="773c5-283">Varsayılan `ForwardLimit` `1` değer (bir), bu nedenle değeri `ForwardLimit` artırılmadığı sürece üstbilgiden yalnızca en doğru değer işlenir.</span><span class="sxs-lookup"><span data-stu-id="773c5-283">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="773c5-284">İdegöreli üstbilgi işlenmeden önce `KnownProxies` `KnownNetworks` isteğin orijinal uzak IP'si veya listelerdeki bir girişle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="773c5-284">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="773c5-285">Bu, ileticileri güvenilmeyen vekillerden kabul etmeyerak başlık sızdırmayı sınırlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-285">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="773c5-286">Bilinmeyen bir proxy algılandığında, günlüğe kaydetme proxy'nin adresini gösterir:</span><span class="sxs-lookup"><span data-stu-id="773c5-286">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="773c5-287">Önceki örnekte, 10.0.0.100 bir proxy sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="773c5-287">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="773c5-288">Sunucu güvenilir bir proxy ise, sunucunun IP adresini `KnownProxies` ekleyin (veya güvenilir `KnownNetworks`bir `Startup.ConfigureServices`ağ ekleyin) içinde.</span><span class="sxs-lookup"><span data-stu-id="773c5-288">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="773c5-289">Daha fazla bilgi [için, İlliyeli Üstbilgi Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-289">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="773c5-290">Yalnızca güvenilen yakınlık ve ağların üstbilgi ilerisine girmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="773c5-290">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="773c5-291">Aksi takdirde, [IP sızdırma saldırıları mümkündür.](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="773c5-291">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="773c5-292">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="773c5-292">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="773c5-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Temel Ayrıcalık Yüksekliği Güvenlik Açığı</span><span class="sxs-lookup"><span data-stu-id="773c5-293">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="773c5-294">ASP.NET Core için önerilen yapılandırmada uygulama IIS/ASP.NET Core Module, Nginx veya Apache kullanılarak barındırılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-294">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="773c5-295">Proxy sunucular, yük dengeleyicileri ve diğer ağ cihazları genellikle uygulamaya ulaşmadan önce istek le ilgili bilgileri belirsiz hale getirir:</span><span class="sxs-lookup"><span data-stu-id="773c5-295">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="773c5-296">HTTPS istekleri HTTP üzerinden iliştirildiğinde, özgün düzen (HTTPS) kaybolur ve bir üstbilgide iletilmelidir.</span><span class="sxs-lookup"><span data-stu-id="773c5-296">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="773c5-297">Bir uygulama, Internet veya şirket ağındaki gerçek kaynağından değil, proxy'den bir istek aldığından, kaynak olan istemci IP adreside de üstbilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="773c5-297">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="773c5-298">Bu bilgiler, yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konumlandırma gibi istek işlemede önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-298">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="773c5-299">İleriye dönük üstbilgi</span><span class="sxs-lookup"><span data-stu-id="773c5-299">Forwarded headers</span></span>

<span data-ttu-id="773c5-300">Sözleşmeye göre, vekiller BILGILERI HTTP üstbilgilerine iletir.</span><span class="sxs-lookup"><span data-stu-id="773c5-300">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="773c5-301">Üst bilgi</span><span class="sxs-lookup"><span data-stu-id="773c5-301">Header</span></span> | <span data-ttu-id="773c5-302">Açıklama</span><span class="sxs-lookup"><span data-stu-id="773c5-302">Description</span></span> |
| ------ | ----------- |
| <span data-ttu-id="773c5-303">X-Forwarded-For</span><span class="sxs-lookup"><span data-stu-id="773c5-303">X-Forwarded-For</span></span> | <span data-ttu-id="773c5-304">İsteği başlatan istemci ve sonraki yakınlıklar hakkında bilgileri bir yakınlık lar zincirinde tutar.</span><span class="sxs-lookup"><span data-stu-id="773c5-304">Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="773c5-305">Bu parametre IP adresleri (ve isteğe bağlı olarak, bağlantı noktası numaraları) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-305">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="773c5-306">Proxy sunucuları zincirinde, ilk parametre isteğiilk yapıldığı istemciyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="773c5-306">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="773c5-307">Sonraki proxy tanımlayıcıları izleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-307">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="773c5-308">Zincirdeki son proxy parametreler listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="773c5-308">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="773c5-309">Son proxy'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanındaki uzak IP adresi olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-309">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> |
| <span data-ttu-id="773c5-310">X-İlli-Proto</span><span class="sxs-lookup"><span data-stu-id="773c5-310">X-Forwarded-Proto</span></span> | <span data-ttu-id="773c5-311">Kaynak düzenin in değeri (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="773c5-311">The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="773c5-312">İstek birden çok yakınlık arasında geçtiyse, değer de şemaları listesi olabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-312">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> |
| <span data-ttu-id="773c5-313">X-Forwarded-Host</span><span class="sxs-lookup"><span data-stu-id="773c5-313">X-Forwarded-Host</span></span> | <span data-ttu-id="773c5-314">Ana Bilgisayar üstbilgi alanının özgün değeri.</span><span class="sxs-lookup"><span data-stu-id="773c5-314">The original value of the Host header field.</span></span> <span data-ttu-id="773c5-315">Genellikle, yakınlık ana bilgisayar üstbilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-315">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="773c5-316">Proxy'nin Ana Bilgisayar üstbilgilerini doğrulayamadığı veya bilinen iyi değerlerle sınırlamadığı sistemleri etkileyen ayrıcalıkların yükseltisi güvenlik açığı hakkında bilgi almak için [Microsoft Güvenlik Danışma Belgesi CVE-2018-0787'ye](https://github.com/aspnet/Announcements/issues/295) bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-316">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="773c5-317">[Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketinden Iletilen Üstbilgi Middleware, bu başlıkları okur ve ilgili <xref:Microsoft.AspNetCore.Http.HttpContext>alanları doldurur.</span><span class="sxs-lookup"><span data-stu-id="773c5-317">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="773c5-318">Ara yazılım güncellemeleri:</span><span class="sxs-lookup"><span data-stu-id="773c5-318">The middleware updates:</span></span>

* <span data-ttu-id="773c5-319">Başlık değerini kullanarak `X-Forwarded-For` [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set.</span><span class="sxs-lookup"><span data-stu-id="773c5-319">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress) &ndash; Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="773c5-320">Ek ayarlar, ara yazılımın nasıl ayarlanır' ı `RemoteIpAddress`etkiler.</span><span class="sxs-lookup"><span data-stu-id="773c5-320">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="773c5-321">Ayrıntılar [için, İlezli Üstbilgiler Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-321">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="773c5-322">Başlık değerini kullanarak `X-Forwarded-Proto` [HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set.</span><span class="sxs-lookup"><span data-stu-id="773c5-322">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme) &ndash; Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="773c5-323">Başlık değerini kullanarak `X-Forwarded-Host` [HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set.</span><span class="sxs-lookup"><span data-stu-id="773c5-323">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host) &ndash; Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="773c5-324">İlezli Üstbilgi Middleware [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-324">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="773c5-325">Varsayılan ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="773c5-325">The default settings are:</span></span>

* <span data-ttu-id="773c5-326">Uygulama ve isteklerin kaynağı arasında yalnızca *bir proxy* vardır.</span><span class="sxs-lookup"><span data-stu-id="773c5-326">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="773c5-327">Yalnızca geri dönüş adresleri bilinen yakınlıklar ve bilinen ağlar için yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-327">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="773c5-328">İlerlenen üstbilgi `X-Forwarded-For` adlandırılmış ve `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="773c5-328">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="773c5-329">Tüm ağ cihazları `X-Forwarded-For` ek `X-Forwarded-Proto` yapılandırma olmadan üstbilgi ve üstbilgi eklemez.</span><span class="sxs-lookup"><span data-stu-id="773c5-329">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="773c5-330">Yakın istekleri uygulamaya ulaştıklarında bu üstbilgileri içermiyorsa, cihaz üreticinizin kılavuzuna başvurun.</span><span class="sxs-lookup"><span data-stu-id="773c5-330">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="773c5-331">Cihaz, cihazdan `X-Forwarded-For` farklı üstbilgi `X-Forwarded-Proto`adları <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> kullanıyorsa ve cihaz tarafından kullanılan üstbilgi adlarıyla eşleşecek <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> şekilde ve seçenekleri ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="773c5-331">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="773c5-332">Daha fazla bilgi için, [farklı üstbilgi adları kullanan bir proxy için](#configuration-for-a-proxy-that-uses-different-header-names) [Iletili Üstbilgiler Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) ve Yapılandırma'ya bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-332">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="773c5-333">IIS/IIS Express ve ASP.NET Çekirdek Modülü</span><span class="sxs-lookup"><span data-stu-id="773c5-333">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="773c5-334">İleşli Üstbilgi Araware, uygulama IIS ve ASP.NET Çekirdek Modülü' nün arkasında [sürücü](xref:host-and-deploy/iis/index#out-of-process-hosting-model) lük sınır dışında barındırılırken Varsayılan olarak [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-334">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="773c5-335">İleli Üstbilgi Middleware, iletili üstdeğerlere [(örneğin, IP sızdırma)](https://www.iplocation.net/ip-spoofing)güven endişeleri nedeniyle ASP.NET Çekirdek Modülüne özgü sınırlı bir yapılandırmaile ilk olarak ara yazılım boru hattında çalışacak şekilde etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-335">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="773c5-336">Ara yazılım, üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi ileri ye doğru yapılandırılır ve tek bir localhost proxy ile sınırlıdır.</span><span class="sxs-lookup"><span data-stu-id="773c5-336">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="773c5-337">Ek yapılandırma gerekiyorsa, [İleriye Dönük Üstbilgi Ara Yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-337">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="773c5-338">Diğer proxy sunucusu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="773c5-338">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="773c5-339">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma yaparken [IIS Tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) kullanmanın dışında, Iletili Üstbilgi Middleware varsayılan olarak etkinleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-339">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="773c5-340">İleşli Üstbilgi Ara ware, bir uygulamanın <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>iletilir üstbilgisini ' ile işlemesi için etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="773c5-340">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="773c5-341">Middleware'e hayır <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> belirtilmemişse ara yazılımı etkinleştirdikten sonra, varsayılan [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) [forwardedheaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span><span class="sxs-lookup"><span data-stu-id="773c5-341">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="773c5-342">Aracı, 'deki <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` üstbilgi ve `X-Forwarded-Proto` üstileri `Startup.ConfigureServices`iletmek için yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-342">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="773c5-343">Diğer <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> ara `Startup.Configure` yazılımları çağırmadan önce yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="773c5-343">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="773c5-344">Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> veya doğrudan `Startup.ConfigureServices` uzantısı yöntemi nde <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>belirtilmişise , ileri varsayılan üstbilgi [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)vardır.</span><span class="sxs-lookup"><span data-stu-id="773c5-344">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="773c5-345">Özellik <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> ileri ye doğru üstbilgi ile yapılandırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="773c5-345">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="773c5-346">Nginx yapılandırması</span><span class="sxs-lookup"><span data-stu-id="773c5-346">Nginx configuration</span></span>

<span data-ttu-id="773c5-347">Üstbilgi `X-Forwarded-For` ve `X-Forwarded-Proto` üstbilgi <xref:host-and-deploy/linux-nginx#configure-nginx>ileri almak için bkz.</span><span class="sxs-lookup"><span data-stu-id="773c5-347">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="773c5-348">Daha fazla bilgi için [NGINX: İleride üstbilgiyi kullanma.](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/)</span><span class="sxs-lookup"><span data-stu-id="773c5-348">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="773c5-349">Apache yapılandırması</span><span class="sxs-lookup"><span data-stu-id="773c5-349">Apache configuration</span></span>

<span data-ttu-id="773c5-350">`X-Forwarded-For`otomatik olarak eklenir [(bkz. Apache Modülü mod_proxy: Proxy İstek Üstbilgilerini Ters](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)Çevir ).</span><span class="sxs-lookup"><span data-stu-id="773c5-350">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="773c5-351">Üstbilginin nasıl iletilen `X-Forwarded-Proto` <xref:host-and-deploy/linux-apache#configure-apache>hakkında bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="773c5-351">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="773c5-352">İleşli Üstbilgi Ara ware seçenekleri</span><span class="sxs-lookup"><span data-stu-id="773c5-352">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="773c5-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen Üstbilgi Middleware davranışını kontrol edin.</span><span class="sxs-lookup"><span data-stu-id="773c5-353"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="773c5-354">Aşağıdaki örnekte varsayılan değerleri değiştirir:</span><span class="sxs-lookup"><span data-stu-id="773c5-354">The following example changes the default values:</span></span>

* <span data-ttu-id="773c5-355">İlerlenen üstbilgideki giriş sayısını `2`' la sınırlandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-355">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="773c5-356">`127.0.10.1`Bilinen bir proxy adresi ekleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-356">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="773c5-357">İdeyiş üstbilgi adını `X-Forwarded-For` varsayılandan ' a `X-Forwarded-For-My-Custom-Header-Name`çevirin.</span><span class="sxs-lookup"><span data-stu-id="773c5-357">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="773c5-358">Seçenek</span><span class="sxs-lookup"><span data-stu-id="773c5-358">Option</span></span> | <span data-ttu-id="773c5-359">Açıklama</span><span class="sxs-lookup"><span data-stu-id="773c5-359">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | <span data-ttu-id="773c5-360">Ana bilgisayarları `X-Forwarded-Host` üstbilgi yle sağlanan değerlerle sınırlandırıyor.</span><span class="sxs-lookup"><span data-stu-id="773c5-360">Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="773c5-361">Değerler, ordinal-ignore-case kullanılarak karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-361">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="773c5-362">Bağlantı noktası numaraları hariç tutulmalıdır.</span><span class="sxs-lookup"><span data-stu-id="773c5-362">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="773c5-363">Liste boşsa, tüm ana bilgisayarlara izin verilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-363">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="773c5-364">Üst düzey joker `*` karakter, boş olmayan tüm ana bilgisayarları sağlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-364">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="773c5-365">Alt etki alanı joker kartlarına izin verilir, ancak kök etki alanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-365">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="773c5-366">Örneğin, `*.contoso.com` alt etki `foo.contoso.com` alanı eşleşir, `contoso.com`ancak kök etki alanı .</span><span class="sxs-lookup"><span data-stu-id="773c5-366">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="773c5-367">Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [Punycode'a](https://tools.ietf.org/html/rfc3492) dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="773c5-367">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="773c5-368">[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı parantezler içermelidir ve [geleneksel](https://tools.ietf.org/html/rfc4291#section-2.2) formda `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`olmalıdır (örneğin, ).</span><span class="sxs-lookup"><span data-stu-id="773c5-368">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="773c5-369">IPv6 adresleri, farklı biçimler arasındaki mantıksal eşitliği denetlemek için özel olarak belirtilmemiştir ve hiçbir kanonializasyon gerçekleştirilmez.</span><span class="sxs-lookup"><span data-stu-id="773c5-369">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="773c5-370">İzin verilen ana bilgisayarların kısıtlanmasına izin verilmese, saldırgan hizmet tarafından oluşturulan bağlantıları sızdırmasına neden olabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-370">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="773c5-371">Varsayılan değer boştur. `IList<string>`</span><span class="sxs-lookup"><span data-stu-id="773c5-371">The default value is an empty `IList<string>`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | <span data-ttu-id="773c5-372">[ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-372">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="773c5-373">Bu seçenek, proxy/forwarder `X-Forwarded-For` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-373">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="773c5-374">Varsayılan değer: `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="773c5-374">The default is `X-Forwarded-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | <span data-ttu-id="773c5-375">Hangi iletmelerin işlenmesi gerektiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="773c5-375">Identifies which forwarders should be processed.</span></span> <span data-ttu-id="773c5-376">Geçerli alanların listesi için [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-376">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="773c5-377">Bu özelliğe atanan `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`tipik değerler.</span><span class="sxs-lookup"><span data-stu-id="773c5-377">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="773c5-378">Varsayılan değer [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)olduğunu.</span><span class="sxs-lookup"><span data-stu-id="773c5-378">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | <span data-ttu-id="773c5-379">[ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-379">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="773c5-380">Bu seçenek, proxy/forwarder `X-Forwarded-Host` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-380">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="773c5-381">Varsayılan değer: `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="773c5-381">The default is `X-Forwarded-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | <span data-ttu-id="773c5-382">[ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-382">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="773c5-383">Bu seçenek, proxy/forwarder `X-Forwarded-Proto` üstbilgiyi kullanmadığında ancak bilgileri iletmek için başka bir üstbilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="773c5-383">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="773c5-384">Varsayılan değer: `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="773c5-384">The default is `X-Forwarded-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | <span data-ttu-id="773c5-385">İşlenen üstbilgideki giriş sayısını sınırlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-385">Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="773c5-386">Sınırı devre dışı bırakacak şekilde ayarlayın, ancak `KnownProxies` bu `KnownNetworks` yalnızca yapılandırıldıysa veya yapılandırılırsa yapılmalıdır. `null`</span><span class="sxs-lookup"><span data-stu-id="773c5-386">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="773c5-387">`null` Değer olmayan bir ayar, ağdaki yan kanallardan gelen yanlış yapılandırılmış yardımcılara ve kötü amaçlı isteklere karşı korunmak için bir önlemdir (ancak garanti değildir).</span><span class="sxs-lookup"><span data-stu-id="773c5-387">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="773c5-388">İlli Üstbilgi Middleware, üstleri sağdan sola doğru ters sırada işler.</span><span class="sxs-lookup"><span data-stu-id="773c5-388">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="773c5-389">Varsayılan değer (`1`) kullanılırsa, değeri `ForwardLimit` artırılmadığı sürece yalnızca üstbilgiden en doğru değer işlenir.</span><span class="sxs-lookup"><span data-stu-id="773c5-389">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="773c5-390">Varsayılan değer: `1`.</span><span class="sxs-lookup"><span data-stu-id="773c5-390">The default is `1`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | <span data-ttu-id="773c5-391">İleriye dönük üstbilgileri kabul etmek için bilinen ağların adres aralıkları.</span><span class="sxs-lookup"><span data-stu-id="773c5-391">Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="773c5-392">Sınıfsız Etki Alanı Yönlendirme (CIDR) gösterimini kullanarak IP aralıkları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="773c5-392">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="773c5-393">Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-393">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="773c5-394">Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="773c5-394">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="773c5-395">[HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-395">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="773c5-396">Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-396">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="773c5-397">Varsayılan değer, `IList` \< <xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> '' için `IPAddress.Loopback`tek bir giriş içeren bir>.</span><span class="sxs-lookup"><span data-stu-id="773c5-397">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | <span data-ttu-id="773c5-398">İleriye dönük üstbilgi kabul etmek için bilinen yakınlık adresleri.</span><span class="sxs-lookup"><span data-stu-id="773c5-398">Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="773c5-399">Tam `KnownProxies` IP adresi eşleşmelerini belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-399">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="773c5-400">Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1`IPv6 olarak temsil edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-400">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="773c5-401">Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="773c5-401">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="773c5-402">[HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-402">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="773c5-403">Daha fazla bilgi [için, IPv6 adresi bölümü olarak temsil edilen bir IPv4 adresi yapılandırması'na](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-403">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="773c5-404">Varsayılan değer, `IList` \< <xref:System.Net.IPAddress> '' için `IPAddress.IPv6Loopback`tek bir giriş içeren bir>.</span><span class="sxs-lookup"><span data-stu-id="773c5-404">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | <span data-ttu-id="773c5-405">[ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-405">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="773c5-406">Varsayılan değer: `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="773c5-406">The default is `X-Original-For`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | <span data-ttu-id="773c5-407">[ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-407">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="773c5-408">Varsayılan değer: `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="773c5-408">The default is `X-Original-Host`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | <span data-ttu-id="773c5-409">[ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgi kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-409">Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="773c5-410">Varsayılan değer: `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="773c5-410">The default is `X-Original-Proto`.</span></span> |
| <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | <span data-ttu-id="773c5-411">Üstbilgi değerlerinin sayısının, işlenen [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitolmasını zorunlu kılmasını.</span><span class="sxs-lookup"><span data-stu-id="773c5-411">Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="773c5-412">Core 1.x ASP.NET varsayılan `true`.</span><span class="sxs-lookup"><span data-stu-id="773c5-412">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="773c5-413">Core 2.0 veya sonraki ASP.NET `false`varsayılan.</span><span class="sxs-lookup"><span data-stu-id="773c5-413">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="773c5-414">Senaryolar ve kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="773c5-414">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="773c5-415">İleiletiüstleri eklemek mümkün olmadığında ve tüm isteklerin güvenli olduğu</span><span class="sxs-lookup"><span data-stu-id="773c5-415">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="773c5-416">Bazı durumlarda, uygulamaya iledilen üstbilgi eklemek mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-416">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="773c5-417">Proxy tüm genel dış isteklerin HTTPS olduğunu zorluyorsa, şema `Startup.Configure` herhangi bir ara yazılım türü kullanmadan önce el ile ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="773c5-417">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="773c5-418">Bu kod, bir geliştirme veya evreleme ortamında bir ortam değişkeni veya başka bir yapılandırma ayarı ile devre dışı alınabilir.</span><span class="sxs-lookup"><span data-stu-id="773c5-418">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="773c5-419">İstek yolunu değiştiren yol tabanı ve yakınlıklarla başa çık</span><span class="sxs-lookup"><span data-stu-id="773c5-419">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="773c5-420">Bazı yakınlıklar yolu sağlam bir şekilde geçer, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama tabanı yolu ile.</span><span class="sxs-lookup"><span data-stu-id="773c5-420">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="773c5-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware [httprequest.path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine uygulama temel yolu içine yol böler.</span><span class="sxs-lookup"><span data-stu-id="773c5-421">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="773c5-422">Proxy `/foo` `/foo/api/1`yolu olarak geçirilen bir proxy yolu için uygulama `Request.PathBase` `/foo` temel `Request.Path` `/api/1` yolu ise, ara yazılım aşağıdaki komutla ve ayarlar:</span><span class="sxs-lookup"><span data-stu-id="773c5-422">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="773c5-423">Orta yazılım ters olarak yeniden çağrıldığında özgün yol ve yol tabanı yeniden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="773c5-423">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="773c5-424">Ara yazılım siparişi işleme hakkında <xref:fundamentals/middleware/index>daha fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="773c5-424">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="773c5-425">Proxy yolu kırparsa (örneğin, `/foo/api/1` `/api/1`iletilmesi), isteğin [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yönlendirmeleri ve bağlantıları düzeltin:</span><span class="sxs-lookup"><span data-stu-id="773c5-425">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="773c5-426">Proxy yol verileri ekliyorsa, <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> özelliği kullanarak ve atayarak yeniden yönlendirmeleri ve bağlantıları düzeltmek için yolun bir kısmını atın:</span><span class="sxs-lookup"><span data-stu-id="773c5-426">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="773c5-427">Farklı üstbilgi adları kullanan bir proxy için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="773c5-427">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="773c5-428">Proxy `X-Forwarded-For` adlandırılmış üstbilgileri kullanmıyorsa `X-Forwarded-Proto` ve proxy adresi/bağlantı noktası ve kaynak <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> düzeni <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> bilgilerini iletmek için, proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ve seçenekleri ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="773c5-428">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="773c5-429">IPv6 adresi olarak temsil edilen bir IPv4 adresi için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="773c5-429">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="773c5-430">Sunucu çift modlu soketler kullanıyorsa, IPv4 adresleri IPv6 biçiminde verilir `10.0.0.1` (örneğin, IPv4'te `::ffff:10.0.0.1` IPv6 olarak veya `::ffff:a00:1`olarak temsil edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-430">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="773c5-431">Bkz. [IPAddress.maptoipv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="773c5-431">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="773c5-432">[HttpContext.Connection.RemoteIpAddress'e](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)bakarak bu biçimin gerekli olup olmadığını belirleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-432">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="773c5-433">Aşağıdaki örnekte, iletilen üstbilgileri sağlayan bir ağ `KnownNetworks` adresi IPv6 biçiminde listeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="773c5-433">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="773c5-434">IPv4 adresi:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="773c5-434">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="773c5-435">Dönüştürülmüş IPv6 adresi:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="773c5-435">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="773c5-436">Dönüştürülmüş önek uzunluğu: 104</span><span class="sxs-lookup"><span data-stu-id="773c5-436">Converted prefix length: 104</span></span>

<span data-ttu-id="773c5-437">Adresi hexadecimal formatta da sağlayabilirsiniz (IPv6'da`10.11.12.1` temsil `::ffff:0a0b:0c01`edilir).</span><span class="sxs-lookup"><span data-stu-id="773c5-437">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="773c5-438">Bir IPv4 adresini IPv6'ya dönüştürürken, ek`8` `::ffff:` IPv6 önekini (8 + 96 = 104) hesaba katmak için CIDR Önek Uzunluğu'na (örnekte) 96 ekleyin.</span><span class="sxs-lookup"><span data-stu-id="773c5-438">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="773c5-439">Linux ve IIS olmayan ters vekiller için şemayı iletin</span><span class="sxs-lookup"><span data-stu-id="773c5-439">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="773c5-440">Bir Azure <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Linux Uygulama Hizmetine, Azure Linux sanal makinesine (VM) veya IIS dışındaki diğer ters proxy'nin arkasına dağıtılırsa, bir siteyi arayan ve sonsuz bir döngüye sokan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="773c5-440">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="773c5-441">TLS ters proxy tarafından sonlandırılır ve Kestrel doğru istek şeması ndan haberdar yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="773c5-441">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="773c5-442">OAuth ve OIDC de yanlış yönlendirmeler oluşturdukları için bu yapılandırmada başarısız olurlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-442">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="773c5-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS'nin arkasında çalışırken İleriye Dönük Üstbilgi Middleware ekler ve yapılandırır, ancak Linux için eşleşen bir otomatik yapılandırma yoktur (Apache veya Nginx entegrasyonu).</span><span class="sxs-lookup"><span data-stu-id="773c5-443"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="773c5-444">Düzeni IIS olmayan senaryolarda proxy'den iletmek için, İleriye Dönük Üstbilgi Ara Ware ekleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="773c5-444">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="773c5-445">In `Startup.ConfigureServices`, aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="773c5-445">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="773c5-446">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="773c5-446">Troubleshoot</span></span>

<span data-ttu-id="773c5-447">Üstbilgi beklendiği gibi iletilmediğinde, [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="773c5-447">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="773c5-448">Günlükler sorunu gidermek için yeterli bilgi sağlamazsa, sunucu tarafından alınan istek üstbilgilerini kaydedin.</span><span class="sxs-lookup"><span data-stu-id="773c5-448">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="773c5-449">İstek üstbilgilerini bir uygulama yanıtına yazmak veya üstbilgigünlerini günlüğe kaydetmek için satır içi ara yazılım kullanın.</span><span class="sxs-lookup"><span data-stu-id="773c5-449">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="773c5-450">Üstbilgileri uygulamanın yanıtına yazmak için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> aşağıdaki terminal satır ara sını aşağıdaki `Startup.Configure`çağrıdan hemen sonra yerleştirin:</span><span class="sxs-lookup"><span data-stu-id="773c5-450">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="773c5-451">Yanıt gövdesi yerine günlüklere yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="773c5-451">You can write to logs instead of the response body.</span></span> <span data-ttu-id="773c5-452">Günlüklere yazma, hata ayıklama sırasında sitenin normal çalışmasını sağlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-452">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="773c5-453">Yanıt gövdesiyerine günlükyazmak için:</span><span class="sxs-lookup"><span data-stu-id="773c5-453">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="773c5-454">Başlangıç'ta `Startup` create [günlüklerinde](xref:fundamentals/logging/index#create-logs-in-startup)açıklandığı gibi sınıfa enjekte edin. `ILogger<Startup>`</span><span class="sxs-lookup"><span data-stu-id="773c5-454">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="773c5-455">Aşağıdaki satır lı ara yazılımı aramadan <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure`hemen sonra yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="773c5-455">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="773c5-456">İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler ' `X-Original-{For|Proto|Host}`e taşınır.</span><span class="sxs-lookup"><span data-stu-id="773c5-456">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="773c5-457">Belirli bir üstbilgide birden çok değer varsa, İlliyeli Üstbilgi Orta Ware üstileri sağdan sola doğru ters sırada işler.</span><span class="sxs-lookup"><span data-stu-id="773c5-457">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="773c5-458">Varsayılan `ForwardLimit` `1` değer (bir), bu nedenle değeri `ForwardLimit` artırılmadığı sürece üstbilgiden yalnızca en doğru değer işlenir.</span><span class="sxs-lookup"><span data-stu-id="773c5-458">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="773c5-459">İdegöreli üstbilgi işlenmeden önce `KnownProxies` `KnownNetworks` isteğin orijinal uzak IP'si veya listelerdeki bir girişle eşleşmelidir.</span><span class="sxs-lookup"><span data-stu-id="773c5-459">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="773c5-460">Bu, ileticileri güvenilmeyen vekillerden kabul etmeyerak başlık sızdırmayı sınırlar.</span><span class="sxs-lookup"><span data-stu-id="773c5-460">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="773c5-461">Bilinmeyen bir proxy algılandığında, günlüğe kaydetme proxy'nin adresini gösterir:</span><span class="sxs-lookup"><span data-stu-id="773c5-461">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="773c5-462">Önceki örnekte, 10.0.0.100 bir proxy sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="773c5-462">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="773c5-463">Sunucu güvenilir bir proxy ise, sunucunun IP adresini `KnownProxies` ekleyin (veya güvenilir `KnownNetworks`bir `Startup.ConfigureServices`ağ ekleyin) içinde.</span><span class="sxs-lookup"><span data-stu-id="773c5-463">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="773c5-464">Daha fazla bilgi [için, İlliyeli Üstbilgi Ara Yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="773c5-464">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="773c5-465">Yalnızca güvenilen yakınlık ve ağların üstbilgi ilerisine girmesine izin verin.</span><span class="sxs-lookup"><span data-stu-id="773c5-465">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="773c5-466">Aksi takdirde, [IP sızdırma saldırıları mümkündür.](https://www.iplocation.net/ip-spoofing)</span><span class="sxs-lookup"><span data-stu-id="773c5-466">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="773c5-467">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="773c5-467">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="773c5-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Temel Ayrıcalık Yüksekliği Güvenlik Açığı</span><span class="sxs-lookup"><span data-stu-id="773c5-468">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
