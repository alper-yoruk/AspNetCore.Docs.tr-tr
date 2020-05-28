---
<span data-ttu-id="8d4cf-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-102">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-103">'Identity'</span></span>
- <span data-ttu-id="8d4cf-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-105">'Razor'</span></span>
- <span data-ttu-id="8d4cf-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-106">'SignalR' uid:</span></span> 

---
# <a name="configure-aspnet-core-to-work-with-proxy-servers-and-load-balancers"></a><span data-ttu-id="8d4cf-107">Proxy sunucularıyla ve yük dengeleyicilerle çalışacak ASP.NET Core yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8d4cf-107">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>

<span data-ttu-id="8d4cf-108">[Chris](https://github.com/Tratcher) 'e göre</span><span class="sxs-lookup"><span data-stu-id="8d4cf-108">By [Chris Ross](https://github.com/Tratcher)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8d4cf-109">ASP.NET Core için Önerilen yapılandırmada, uygulama IIS/ASP. NET Core modülü, NGINX veya Apache kullanılarak barındırılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-109">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="8d4cf-110">Proxy sunucuları, yük dengeleyiciler ve diğer ağ gereçleri, genellikle istek hakkında uygulamaya ulaşmadan önce bu bilgileri gizlemediğinde:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-110">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="8d4cf-111">HTTPS istekleri HTTP üzerinden proxy olduğunda, özgün düzen (HTTPS) kaybolur ve bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-111">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="8d4cf-112">Bir uygulama, Internet veya şirket ağında gerçek kaynağını değil, ara sunucudan bir istek aldığından, kaynak istemci IP adresinin de bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-112">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="8d4cf-113">Bu bilgiler, istek işlemede, örneğin yeniden yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konum gibi önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-113">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="8d4cf-114">İletilen üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="8d4cf-114">Forwarded headers</span></span>

<span data-ttu-id="8d4cf-115">Kurala göre, proxy 'leri HTTP başlıklarında iletme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-115">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="8d4cf-116">Üst bilgi</span><span class="sxs-lookup"><span data-stu-id="8d4cf-116">Header</span></span> | <span data-ttu-id="8d4cf-117">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d4cf-117">Description</span></span> |
| ---
<span data-ttu-id="8d4cf-118">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-118">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-119">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-119">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-120">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-120">'Identity'</span></span>
- <span data-ttu-id="8d4cf-121">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-121">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-122">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-122">'Razor'</span></span>
- <span data-ttu-id="8d4cf-123">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-123">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-124">--- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-124">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-125">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-125">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-126">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-126">'Identity'</span></span>
- <span data-ttu-id="8d4cf-127">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-127">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-128">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-128">'Razor'</span></span>
- <span data-ttu-id="8d4cf-129">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-129">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-130">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-130">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-131">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-131">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-132">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-132">'Identity'</span></span>
- <span data-ttu-id="8d4cf-133">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-133">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-134">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-134">'Razor'</span></span>
- <span data-ttu-id="8d4cf-135">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-135">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-136">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-136">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-137">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-137">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-138">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-138">'Identity'</span></span>
- <span data-ttu-id="8d4cf-139">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-139">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-140">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-140">'Razor'</span></span>
- <span data-ttu-id="8d4cf-141">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-141">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-142">------ | | X-Iletildi-for | , İsteği başlatan istemciyle ilgili bilgileri ve bir proxy zincirinde sonraki proxy 'leri barındırır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-142">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="8d4cf-143">Bu parametre IP adresleri (ve isteğe bağlı olarak bağlantı noktası numaraları) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-143">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="8d4cf-144">Bir ara sunucu zincirinde, ilk parametre isteğin ilk yaptığı istemciyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-144">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="8d4cf-145">Sonraki proxy tanımlayıcıları izler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-145">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="8d4cf-146">Zincirdeki son proxy, Parametreler listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-146">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="8d4cf-147">Son proxy 'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanında uzak IP adresi olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-147">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="8d4cf-148">| | X-Iletilen-proto | Kaynak düzenin değeri (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-148">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="8d4cf-149">Bu değer, istek birden çok proxy 'ye geçen bir düzen listesi de olabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-149">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="8d4cf-150">| | X-Iletilen-ana bilgisayar | Ana bilgisayar üst bilgisi alanının özgün değeri.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-150">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="8d4cf-151">Genellikle, proxy 'ler ana bilgisayar üst bilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-151">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="8d4cf-152">Proxy 'nin konak üstbilgilerini doğrulamadığı veya kısıtlayamayacağı sistemleri etkileyen ayrıcalık yükselmesi güvenlik açığı hakkında bilgi için bkz. [Microsoft Güvenlik Danışmanlığı CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-152">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="8d4cf-153">[Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki Iletilen üstbilgiler ara yazılımı, bu üst bilgileri okur ve ilgili alanları üzerinde doldurur <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-153">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="8d4cf-154">Ara yazılım güncelleştirmeleri:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-154">The middleware updates:</span></span>

* <span data-ttu-id="8d4cf-155">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-155">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="8d4cf-156">Ek ayarlar, ara yazılımın nasıl ayarlanacağını etkiler `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-156">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="8d4cf-157">Ayrıntılar için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-157">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="8d4cf-158">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-158">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="8d4cf-159">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-159">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="8d4cf-160">İletilen üstbilgiler ara yazılımı [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-160">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="8d4cf-161">Varsayılan ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-161">The default settings are:</span></span>

* <span data-ttu-id="8d4cf-162">Uygulama ve isteklerin kaynağı arasında yalnızca *bir ara sunucu* vardır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-162">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="8d4cf-163">Bilinen proxy 'ler ve bilinen ağlar için yalnızca geri döngü adresleri yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-163">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="8d4cf-164">İletilen üstbilgiler ve olarak adlandırılır `X-Forwarded-For` `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-164">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="8d4cf-165">Tüm ağ gereçleri `X-Forwarded-For` `X-Forwarded-Proto` ek yapılandırma olmadan ve üst bilgilerini eklemez.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-165">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="8d4cf-166">Proxy istekler uygulamaya ulaştığında bu üst bilgileri içermiyorsa, Gereç üreticinizin kılavuzuna başvurun.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-166">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="8d4cf-167">Gereç, ve dışındaki farklı üstbilgi adlarını kullanıyorsa `X-Forwarded-For` `X-Forwarded-Proto` , <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini gereç tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-167">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="8d4cf-168">Daha fazla bilgi için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) ve [farklı üstbilgi adları kullanan bir proxy için yapılandırma](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-168">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="8d4cf-169">IIS/IIS Express ve ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="8d4cf-169">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="8d4cf-170">İletilen üstbilgiler ara yazılımı, uygulama IIS 'nin arkasında ve ASP.NET Core modülünden sonra [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) barındırılıyorsa [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-170">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="8d4cf-171">İletilen üstbilgiler ara yazılımı, iletilen üst bilgilerle (örneğin, [IP aldatmacası](https://www.iplocation.net/ip-spoofing)) güven sorunları nedeniyle ASP.NET Core modülüne özgü kısıtlanmış bir yapılandırmaya sahip olan ara yazılım ardışık düzeninde ilk olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-171">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="8d4cf-172">Ara yazılım, `X-Forwarded-For` ve üst bilgilerini iletmek üzere yapılandırılmıştır `X-Forwarded-Proto` ve tek bir localhost proxy ile kısıtlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-172">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="8d4cf-173">Ek yapılandırma gerekliyse, [Iletilen üstbilgiler ara yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-173">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="8d4cf-174">Diğer proxy sunucu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="8d4cf-174">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="8d4cf-175">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında [IIS tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 'nin kullanılması dışında, iletilen üstbilgiler ara yazılımı varsayılan olarak etkinleştirilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-175">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="8d4cf-176">İletilen üstbilgileri ile işlemek için bir uygulamanın yönlendirilmiş üst bilgi ara yazılımı etkinleştirilmelidir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-176">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="8d4cf-177">Ara yazılım için Hayır belirtilmemişse, ara yazılımı etkinleştirdikten sonra <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> varsayılan [Forwardedheadersoptions. Forwardedheaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) , [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-177">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="8d4cf-178">İle ara yazılımı, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` içindeki ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-178">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8d4cf-179"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` Diğer ara yazılım çağrılmadan önce metodunu çağırın:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-179">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="8d4cf-180">Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `Startup.ConfigureServices` veya doğrudan genişletme yöntemine belirtilmemişse <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , iletmek Için varsayılan üstbilgiler [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-180">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="8d4cf-181"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Özelliğin, iletmek için üst bilgilerle yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-181">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="8d4cf-182">NGINX yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8d4cf-182">Nginx configuration</span></span>

<span data-ttu-id="8d4cf-183">`X-Forwarded-For`Ve `X-Forwarded-Proto` üst bilgilerini iletmek için bkz <xref:host-and-deploy/linux-nginx#configure-nginx> ..</span><span class="sxs-lookup"><span data-stu-id="8d4cf-183">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="8d4cf-184">Daha fazla bilgi için bkz. [NGINX: iletilen üstbilgiyi kullanma](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-184">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="8d4cf-185">Apache yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8d4cf-185">Apache configuration</span></span>

<span data-ttu-id="8d4cf-186">`X-Forwarded-For`otomatik olarak eklenir (bkz. [Apache modülü mod_proxy: ters proxy Istek üstbilgileri](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-186">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="8d4cf-187">Üstbilgiyi iletme hakkında daha fazla bilgi için `X-Forwarded-Proto` bkz <xref:host-and-deploy/linux-apache#configure-apache> ..</span><span class="sxs-lookup"><span data-stu-id="8d4cf-187">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="8d4cf-188">İletilen üstbilgiler ara yazılım seçenekleri</span><span class="sxs-lookup"><span data-stu-id="8d4cf-188">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="8d4cf-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen üstbilgiler ara yazılımı davranışını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-189"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="8d4cf-190">Aşağıdaki örnek varsayılan değerleri değiştirir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-190">The following example changes the default values:</span></span>

* <span data-ttu-id="8d4cf-191">İletilen üst bilgilerdeki giriş sayısını sınırlayın `2` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-191">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="8d4cf-192">Bilinen bir proxy adresi ekleyin `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-192">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="8d4cf-193">İletilen üst bilgi adını varsayılan değerinden olarak değiştirin `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-193">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="8d4cf-194">Seçenek</span><span class="sxs-lookup"><span data-stu-id="8d4cf-194">Option</span></span> | <span data-ttu-id="8d4cf-195">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d4cf-195">Description</span></span> |
| ---
<span data-ttu-id="8d4cf-196">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-196">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-197">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-197">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-198">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-198">'Identity'</span></span>
- <span data-ttu-id="8d4cf-199">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-199">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-200">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-200">'Razor'</span></span>
- <span data-ttu-id="8d4cf-201">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-201">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-202">--- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-202">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-203">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-203">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-204">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-204">'Identity'</span></span>
- <span data-ttu-id="8d4cf-205">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-205">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-206">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-206">'Razor'</span></span>
- <span data-ttu-id="8d4cf-207">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-207">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-208">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-208">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-209">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-210">'Identity'</span></span>
- <span data-ttu-id="8d4cf-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-212">'Razor'</span></span>
- <span data-ttu-id="8d4cf-213">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-214">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-214">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-215">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-215">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-216">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-216">'Identity'</span></span>
- <span data-ttu-id="8d4cf-217">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-217">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-218">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-218">'Razor'</span></span>
- <span data-ttu-id="8d4cf-219">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-219">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ana bilgisayarları üst bilgiyle `X-Forwarded-Host` belirtilen değerlerle sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-220">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="8d4cf-221">Değerler, Ordinal-Ignore-Case kullanılarak karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-221">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="8d4cf-222">Bağlantı noktası numaraları dışlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-222">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="8d4cf-223">Liste boşsa, tüm konaklara izin verilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-223">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="8d4cf-224">Üst düzey joker karakter `*` boş olmayan tüm konaklara izin verir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-224">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="8d4cf-225">Alt etki alanı Joker karakterlere izin verilir ancak kök etki alanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-225">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="8d4cf-226">Örneğin, `*.contoso.com` `foo.contoso.com` kök etki alanını değil, alt etki alanıyla eşleşir `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-226">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="8d4cf-227">Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [puni koduna](https://tools.ietf.org/html/rfc3492) dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-227">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="8d4cf-228">[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı ayraçları içermeli ve [geleneksel biçimde](https://tools.ietf.org/html/rfc4291#section-2.2) olmalıdır (örneğin, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-228">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="8d4cf-229">IPv6 adresleri, farklı biçimler arasında mantıksal eşitlik denetimi için özel değildir ve hiçbir kurallı kullanım yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-229">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="8d4cf-230">İzin verilen konaklar kısıtlanamaması, bir saldırganın hizmet tarafından oluşturulan bağlantıları sızdırmasına izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-230">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="8d4cf-231">Varsayılan değer boş `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-231">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="8d4cf-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-232">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="8d4cf-233">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-For` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-233">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="8d4cf-234">Varsayılan değer: `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-234">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="8d4cf-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Hangi ileticilerin işleneceğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-235">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="8d4cf-236">Uygulanan alanların listesi için [Forwardedheaders numaralandırması](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-236">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="8d4cf-237">Bu özelliğe atanan tipik değerler şunlardır `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-237">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="8d4cf-238">Varsayılan değer [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-238">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="8d4cf-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [Forwardedheadersdefaults varsayılan. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-239">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="8d4cf-240">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Host` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-240">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="8d4cf-241">Varsayılan değer: `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-241">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="8d4cf-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-242">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="8d4cf-243">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Proto` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-243">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="8d4cf-244">Varsayılan değer: `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-244">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="8d4cf-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | İşlenen üst bilgilerdeki giriş sayısını sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-245">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="8d4cf-246">`null`Sınırı devre dışı bırakmak için olarak ayarlayın, ancak bu yalnızca `KnownProxies` veya `KnownNetworks` yapılandırılmışsa yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-246">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="8d4cf-247">Değer olmayan bir `null` önlem, hatalı yapılandırılmış proxy 'lerde ve ağdaki yan kanallardan gelen kötü amaçlı isteklere karşı koruma sağlamak için bir önlem (garanti değil) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-247">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="8d4cf-248">İletilen üstbilgiler ara yazılımı, üst bilgileri sağdan sola doğru sırada işler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-248">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="8d4cf-249">Varsayılan değer ( `1` ) kullanılırsa, değeri artmadığı müddetçe yalnızca üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-249">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="8d4cf-250">Varsayılan değer: `1`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-250">The default is `1`.</span></span> <span data-ttu-id="8d4cf-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | İletilen üstbilgileri kabul etmek için bilinen ağların adres aralıkları.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-251">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="8d4cf-252">Sınıfsız Etki alanları arası yönlendirme (CıDR) gösterimini kullanarak IP aralıkları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-252">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="8d4cf-253">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-253">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="8d4cf-254">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-254">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="8d4cf-255">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-255">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="8d4cf-256">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-256">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="8d4cf-257">Varsayılan, `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> için tek bir giriş içeren bir> `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-257">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="8d4cf-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | İletilen üstbilgileri kabul etmek için bilinen proxy 'lerin adresleri.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-258">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="8d4cf-259">`KnownProxies`Tam IP adresi eşleşmelerini belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-259">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="8d4cf-260">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-260">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="8d4cf-261">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-261">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="8d4cf-262">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-262">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="8d4cf-263">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-263">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="8d4cf-264">Varsayılan, `IList` \<<xref:System.Net.IPAddress> için tek bir giriş içeren bir> `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-264">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="8d4cf-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-265">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="8d4cf-266">Varsayılan değer: `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-266">The default is `X-Original-For`.</span></span> <span data-ttu-id="8d4cf-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [Forwardedheadersdefaults varsayılan. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-267">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="8d4cf-268">Varsayılan değer: `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-268">The default is `X-Original-Host`.</span></span> <span data-ttu-id="8d4cf-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-269">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="8d4cf-270">Varsayılan değer: `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-270">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="8d4cf-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Bilgi işlem sayısının, işlenmekte olan [Forwardedheadersoptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitlenmiş olmasını gerektir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-271">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="8d4cf-272">ASP.NET Core 1. x içindeki varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-272">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="8d4cf-273">ASP.NET Core 2,0 veya sonraki sürümlerde varsayılan değer `false` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-273">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="8d4cf-274">Senaryolar ve kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="8d4cf-274">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="8d4cf-275">İletilen üstbilgiler eklemek mümkün olmadığında ve tüm istekler güvenlidir</span><span class="sxs-lookup"><span data-stu-id="8d4cf-275">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="8d4cf-276">Bazı durumlarda, iletilen üstbilgileri uygulamaya yönelik isteklere eklemek mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-276">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="8d4cf-277">Proxy tüm genel dış isteklerin HTTPS olduğunu zortacaktır, Düzen `Startup.Configure` herhangi bir tür ara yazılım kullanılmadan önce içinde el ile ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-277">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="8d4cf-278">Bu kod bir geliştirme veya hazırlama ortamındaki ortam değişkeniyle veya diğer yapılandırma ayarıyla devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-278">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="8d4cf-279">Yol tabanı ve istek yolunu değiştiren proxy 'ler ile uğraşın</span><span class="sxs-lookup"><span data-stu-id="8d4cf-279">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="8d4cf-280">Bazı proxy 'ler yolu bozulmadan, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama temel yolu ile geçer.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-280">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="8d4cf-281">[Usepathbaseextensions. usepathbase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ara yazılımı, yolu HttpRequest. [Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve uygulama temeli yoluna, [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine böler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-281">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="8d4cf-282">, `/foo` Olarak geçirilen bir ara sunucu yolu için uygulama temel yolu ise `/foo/api/1` , ara yazılım `Request.PathBase` `/foo` `Request.Path` `/api/1` aşağıdaki komutla ve olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-282">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="8d4cf-283">Özgün yol ve yol tabanı, ara yazılım ters ' de çağrıldığında yeniden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-283">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="8d4cf-284">Ara yazılım siparişi işleme hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..</span><span class="sxs-lookup"><span data-stu-id="8d4cf-284">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="8d4cf-285">Proxy, yolu kırpar (örneğin, ' `/foo/api/1` ye iletme `/api/1` ), Isteğin [pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yeniden yönlendirmeleri ve bağlantıları düzeltir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-285">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="8d4cf-286">Proxy, yol verileri ekliyor ise, kullanarak yeniden yönlendirmeleri ve bağlantıları onarmak üzere yolun bir bölümünü atın ve özelliğini kullanarak yeniden yönlendirmeyi ve bağlantıları onarın <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="8d4cf-286">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="8d4cf-287">Farklı üstbilgi adları kullanan bir ara sunucu için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8d4cf-287">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="8d4cf-288">Proxy, adlı üst bilgileri kullanmıyorsa `X-Forwarded-For` ve `X-Forwarded-Proto` proxy adresini/bağlantı noktasını ve kaynak düzen bilgilerini iletmek için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-288">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="8d4cf-289">IPv6 adresi olarak temsil edilen IPv4 adresinin yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8d4cf-289">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="8d4cf-290">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 'da veya olarak temsil edilen IPv4 `::ffff:10.0.0.1` `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-290">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="8d4cf-291">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-291">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="8d4cf-292">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-292">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="8d4cf-293">Aşağıdaki örnekte, iletilen üstbilgiler sağlayan bir ağ adresi `KnownNetworks` listeye IPv6 biçiminde eklenir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-293">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="8d4cf-294">IPv4 adresi:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="8d4cf-294">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="8d4cf-295">Dönüştürülen IPv6 adresi:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="8d4cf-295">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="8d4cf-296">Dönüştürülen önek uzunluğu: 104</span><span class="sxs-lookup"><span data-stu-id="8d4cf-296">Converted prefix length: 104</span></span>

<span data-ttu-id="8d4cf-297">Adresi onaltılık biçimde ( `10.11.12.1` IPv6 olarak temsil edilir) de sağlayabilirsiniz `::ffff:0a0b:0c01` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-297">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="8d4cf-298">Bir IPv4 adresini IPv6 'ya dönüştürürken, `8` ek `::ffff:` IPv6 öneki (8 + 96 = 104) için (ÖRNEKTEKI) CIDR önek uzunluğuna 96 ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-298">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="8d4cf-299">Linux ve IIS olmayan ters proxy 'ler için Düzen iletme</span><span class="sxs-lookup"><span data-stu-id="8d4cf-299">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="8d4cf-300"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Bir azure Linux App Service, Azure Linux sanal MAKINESI (VM) veya IIS 'nin yanı sıra diğer tüm ters proxy 'ler için dağıtıldığında bir siteyi çağıran ve sonsuz döngüye alan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-300">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="8d4cf-301">TLS, ters proxy tarafından sonlandırılır ve Kestrel doğru istek düzeninden haberdar değildir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-301">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="8d4cf-302">OAuth ve OıDC yanlış yeniden yönlendirmeler oluşturduğundan bu yapılandırmada de başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-302">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="8d4cf-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS 'nin arkasında çalışırken Iletilen üstbilgiler ara yazılımı ekler ve yapılandırır, ancak Linux için eşleşen otomatik yapılandırma (Apache veya NGINX tümleştirmesi) yoktur.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-303"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="8d4cf-304">IIS olmayan senaryolarda düzeni proxy 'den iletmek için, Iletilen üstbilgiler ara yazılımını ekleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-304">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="8d4cf-305">İçinde `Startup.ConfigureServices` , aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-305">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="certificate-forwarding"></a><span data-ttu-id="8d4cf-306">Sertifika iletme</span><span class="sxs-lookup"><span data-stu-id="8d4cf-306">Certificate forwarding</span></span> 

### <a name="azure"></a><span data-ttu-id="8d4cf-307">Azure</span><span class="sxs-lookup"><span data-stu-id="8d4cf-307">Azure</span></span>

<span data-ttu-id="8d4cf-308">Sertifika iletme için Azure App Service yapılandırmak için, bkz. [Azure App Service IÇIN TLS karşılıklı kimlik doğrulamasını yapılandırma](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-308">To configure Azure App Service for certificate forwarding, see [Configure TLS mutual authentication for Azure App Service](/azure/app-service/app-service-web-configure-tls-mutual-auth).</span></span> <span data-ttu-id="8d4cf-309">Aşağıdaki kılavuz ASP.NET Core uygulamasını yapılandırmaya aittir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-309">The following guidance pertains to configuring the ASP.NET Core app.</span></span>

<span data-ttu-id="8d4cf-310">İçinde `Startup.Configure` , çağrısının önüne aşağıdaki kodu ekleyin `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="8d4cf-310">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```


<span data-ttu-id="8d4cf-311">Sertifika Iletme ara yazılımını, Azure 'un kullandığı üst bilgi adını belirtecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-311">Configure Certificate Forwarding Middleware to specify the header name that Azure uses.</span></span> <span data-ttu-id="8d4cf-312">İçinde `Startup.ConfigureServices` , ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-312">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "X-ARR-ClientCert");
```

### <a name="other-web-proxies"></a><span data-ttu-id="8d4cf-313">Diğer Web proxy 'leri</span><span class="sxs-lookup"><span data-stu-id="8d4cf-313">Other web proxies</span></span>

<span data-ttu-id="8d4cf-314">IIS veya Azure App Service uygulama Isteği yönlendirme (ARR) olmayan bir ara sunucu kullanılıyorsa, proxy 'yi bir HTTP üst bilgisinde aldığı sertifikayı iletecek şekilde yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-314">If a proxy is used that isn't IIS or Azure App Service's Application Request Routing (ARR), configure the proxy to forward the certificate that it received in an HTTP header.</span></span> <span data-ttu-id="8d4cf-315">İçinde `Startup.Configure` , çağrısının önüne aşağıdaki kodu ekleyin `app.UseAuthentication();` :</span><span class="sxs-lookup"><span data-stu-id="8d4cf-315">In `Startup.Configure`, add the following code before the call to `app.UseAuthentication();`:</span></span>

```csharp
app.UseCertificateForwarding();
```

<span data-ttu-id="8d4cf-316">Üst bilgi adını belirtmek için sertifika Iletme ara yazılımını yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-316">Configure the Certificate Forwarding Middleware to specify the header name.</span></span> <span data-ttu-id="8d4cf-317">İçinde `Startup.ConfigureServices` , ara yazılımın bir sertifika oluşturmasındaki üstbilgiyi yapılandırmak için aşağıdaki kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-317">In `Startup.ConfigureServices`, add the following code to configure the header from which the middleware builds a certificate:</span></span>

```csharp
services.AddCertificateForwarding(options =>
    options.CertificateHeader = "YOUR_CERTIFICATE_HEADER_NAME");
```

<span data-ttu-id="8d4cf-318">Ara sunucu Base64 ile kodlanmazsa (NGINX ile aynı durumda olduğu gibi), `HeaderConverter` seçeneğini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-318">If the proxy isn't base64-encoding the certificate (as is the case with Nginx), set the `HeaderConverter` option.</span></span> <span data-ttu-id="8d4cf-319">İçinde aşağıdaki örneği göz önünde bulundurun `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="8d4cf-319">Consider the following example in `Startup.ConfigureServices`:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="8d4cf-320">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="8d4cf-320">Troubleshoot</span></span>

<span data-ttu-id="8d4cf-321">Üstbilgiler beklendiği gibi iletilemediği zaman [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-321">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8d4cf-322">Günlükler sorunu gidermek için yeterli bilgi sağlamıyorsa, sunucu tarafından alınan istek üstbilgilerini numaralandırın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-322">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="8d4cf-323">Bir uygulama yanıtına istek üst bilgileri yazmak veya üst bilgileri günlüğe kaydetmek için satır içi ara yazılım kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-323">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="8d4cf-324">Üst bilgileri uygulamanın yanıtına yazmak için, aşağıdaki Terminal satır içi ara yazılımını, içindeki çağrısından hemen sonra yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8d4cf-324">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="8d4cf-325">Yanıt gövdesi yerine günlüklere yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-325">You can write to logs instead of the response body.</span></span> <span data-ttu-id="8d4cf-326">Günlüklere yazmak, sitenin hata ayıklanırken normal şekilde çalışmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-326">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="8d4cf-327">Yanıt gövdesi yerine günlükleri yazmak için:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-327">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="8d4cf-328">`ILogger<Startup>` `Startup` [Başlangıçta günlükleri oluşturma](xref:fundamentals/logging/index#create-logs-in-startup)bölümünde açıklandığı gibi sınıfına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-328">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="8d4cf-329">İçindeki çağrısından hemen sonra aşağıdaki satır içi ara yazılımı yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-329">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8d4cf-330">İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler öğesine taşınır `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-330">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="8d4cf-331">Belirli bir üst bilgide birden çok değer varsa, Iletilen üstbilgiler ara yazılımı sağdan sola doğru sırada üst bilgileri işler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-331">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="8d4cf-332">Varsayılan değer `ForwardLimit` `1` (bir) ise, değeri arttırılmadığı müddetçe yalnızca en sağdaki üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-332">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="8d4cf-333">`KnownProxies`İletilen üstbilgiler işlenmeden önce isteğin özgün uzak IP 'si veya listelerindeki bir girdiyle eşleşmesi gerekir `KnownNetworks` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-333">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="8d4cf-334">Bu, güvenilmeyen proxy 'lerden ileticileri kabul etmediği için üst bilgi yanıltmasını kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-334">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="8d4cf-335">Bilinmeyen bir proxy algılandığında günlüğe kaydetme, proxy 'nin adresini gösterir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-335">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="8d4cf-336">Yukarıdaki örnekte, alana 10.0.0.100 bir proxy sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-336">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="8d4cf-337">Sunucu, güvenilir bir ara sunucu ise, sunucusunun IP adresini `KnownProxies` ' e (veya güvenilen bir ağı öğesine ekleyin) ekleyin `KnownNetworks` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-337">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8d4cf-338">Daha fazla bilgi için [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-338">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="8d4cf-339">Yalnızca güvenilen proxy 'lerin ve ağların üstbilgileri iletmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-339">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="8d4cf-340">Aksi takdirde, [IP sahtekarlığı](https://www.iplocation.net/ip-spoofing) saldırıları mümkündür.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-340">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d4cf-341">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8d4cf-341">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="8d4cf-342">Microsoft güvenlik danışmanlık CVE-2018-0787: ayrıcalık yükselmesi güvenlik açığı ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d4cf-342">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8d4cf-343">ASP.NET Core için Önerilen yapılandırmada, uygulama IIS/ASP. NET Core modülü, NGINX veya Apache kullanılarak barındırılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-343">In the recommended configuration for ASP.NET Core, the app is hosted using IIS/ASP.NET Core Module, Nginx, or Apache.</span></span> <span data-ttu-id="8d4cf-344">Proxy sunucuları, yük dengeleyiciler ve diğer ağ gereçleri, genellikle istek hakkında uygulamaya ulaşmadan önce bu bilgileri gizlemediğinde:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-344">Proxy servers, load balancers, and other network appliances often obscure information about the request before it reaches the app:</span></span>

* <span data-ttu-id="8d4cf-345">HTTPS istekleri HTTP üzerinden proxy olduğunda, özgün düzen (HTTPS) kaybolur ve bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-345">When HTTPS requests are proxied over HTTP, the original scheme (HTTPS) is lost and must be forwarded in a header.</span></span>
* <span data-ttu-id="8d4cf-346">Bir uygulama, Internet veya şirket ağında gerçek kaynağını değil, ara sunucudan bir istek aldığından, kaynak istemci IP adresinin de bir üst bilgide iletilmesi gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-346">Because an app receives a request from the proxy and not its true source on the Internet or corporate network, the originating client IP address must also be forwarded in a header.</span></span>

<span data-ttu-id="8d4cf-347">Bu bilgiler, istek işlemede, örneğin yeniden yönlendirmeler, kimlik doğrulama, bağlantı oluşturma, ilke değerlendirmesi ve istemci coğrafi konum gibi önemli olabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-347">This information may be important in request processing, for example in redirects, authentication, link generation, policy evaluation, and client geolocation.</span></span>

## <a name="forwarded-headers"></a><span data-ttu-id="8d4cf-348">İletilen üstbilgiler</span><span class="sxs-lookup"><span data-stu-id="8d4cf-348">Forwarded headers</span></span>

<span data-ttu-id="8d4cf-349">Kurala göre, proxy 'leri HTTP başlıklarında iletme bilgileri.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-349">By convention, proxies forward information in HTTP headers.</span></span>

| <span data-ttu-id="8d4cf-350">Üst bilgi</span><span class="sxs-lookup"><span data-stu-id="8d4cf-350">Header</span></span> | <span data-ttu-id="8d4cf-351">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d4cf-351">Description</span></span> |
| ---
<span data-ttu-id="8d4cf-352">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-352">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-353">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-353">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-354">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-354">'Identity'</span></span>
- <span data-ttu-id="8d4cf-355">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-355">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-356">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-356">'Razor'</span></span>
- <span data-ttu-id="8d4cf-357">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-357">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-358">--- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-358">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-359">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-360">'Identity'</span></span>
- <span data-ttu-id="8d4cf-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-362">'Razor'</span></span>
- <span data-ttu-id="8d4cf-363">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-364">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-364">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-365">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-365">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-366">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-366">'Identity'</span></span>
- <span data-ttu-id="8d4cf-367">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-367">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-368">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-368">'Razor'</span></span>
- <span data-ttu-id="8d4cf-369">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-369">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-370">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-370">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-371">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-371">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-372">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-372">'Identity'</span></span>
- <span data-ttu-id="8d4cf-373">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-373">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-374">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-374">'Razor'</span></span>
- <span data-ttu-id="8d4cf-375">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-375">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-376">------ | | X-Iletildi-for | , İsteği başlatan istemciyle ilgili bilgileri ve bir proxy zincirinde sonraki proxy 'leri barındırır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-376">------ | | X-Forwarded-For | Holds information about the client that initiated the request and subsequent proxies in a chain of proxies.</span></span> <span data-ttu-id="8d4cf-377">Bu parametre IP adresleri (ve isteğe bağlı olarak bağlantı noktası numaraları) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-377">This parameter may contain IP addresses (and, optionally, port numbers).</span></span> <span data-ttu-id="8d4cf-378">Bir ara sunucu zincirinde, ilk parametre isteğin ilk yaptığı istemciyi gösterir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-378">In a chain of proxy servers, the first parameter indicates the client where the request was first made.</span></span> <span data-ttu-id="8d4cf-379">Sonraki proxy tanımlayıcıları izler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-379">Subsequent proxy identifiers follow.</span></span> <span data-ttu-id="8d4cf-380">Zincirdeki son proxy, Parametreler listesinde değil.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-380">The last proxy in the chain isn't in the list of parameters.</span></span> <span data-ttu-id="8d4cf-381">Son proxy 'nin IP adresi ve isteğe bağlı olarak bir bağlantı noktası numarası, aktarım katmanında uzak IP adresi olarak kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-381">The last proxy's IP address, and optionally a port number, are available as the remote IP address at the transport layer.</span></span> <span data-ttu-id="8d4cf-382">| | X-Iletilen-proto | Kaynak düzenin değeri (HTTP/HTTPS).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-382">| | X-Forwarded-Proto | The value of the originating scheme (HTTP/HTTPS).</span></span> <span data-ttu-id="8d4cf-383">Bu değer, istek birden çok proxy 'ye geçen bir düzen listesi de olabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-383">The value may also be a list of schemes if the request has traversed multiple proxies.</span></span> <span data-ttu-id="8d4cf-384">| | X-Iletilen-ana bilgisayar | Ana bilgisayar üst bilgisi alanının özgün değeri.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-384">| | X-Forwarded-Host | The original value of the Host header field.</span></span> <span data-ttu-id="8d4cf-385">Genellikle, proxy 'ler ana bilgisayar üst bilgisini değiştirmez.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-385">Usually, proxies don't modify the Host header.</span></span> <span data-ttu-id="8d4cf-386">Proxy 'nin konak üstbilgilerini doğrulamadığı veya kısıtlayamayacağı sistemleri etkileyen ayrıcalık yükselmesi güvenlik açığı hakkında bilgi için bkz. [Microsoft Güvenlik Danışmanlığı CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-386">See [Microsoft Security Advisory CVE-2018-0787](https://github.com/aspnet/Announcements/issues/295) for information on an elevation-of-privileges vulnerability that affects systems where the proxy doesn't validate or restrict Host headers to known good values.</span></span> |

<span data-ttu-id="8d4cf-387">[Microsoft. AspNetCore. HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) paketindeki Iletilen üstbilgiler ara yazılımı, bu üst bilgileri okur ve ilgili alanları üzerinde doldurur <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-387">The Forwarded Headers Middleware, from the [Microsoft.AspNetCore.HttpOverrides](https://www.nuget.org/packages/Microsoft.AspNetCore.HttpOverrides/) package, reads these headers and fills in the associated fields on <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>

<span data-ttu-id="8d4cf-388">Ara yazılım güncelleştirmeleri:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-388">The middleware updates:</span></span>

* <span data-ttu-id="8d4cf-389">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): `X-Forwarded-For` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-389">[HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress): Set using the `X-Forwarded-For` header value.</span></span> <span data-ttu-id="8d4cf-390">Ek ayarlar, ara yazılımın nasıl ayarlanacağını etkiler `RemoteIpAddress` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-390">Additional settings influence how the middleware sets `RemoteIpAddress`.</span></span> <span data-ttu-id="8d4cf-391">Ayrıntılar için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-391">For details, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>
* <span data-ttu-id="8d4cf-392">[HttpContext. Request. Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): `X-Forwarded-Proto` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-392">[HttpContext.Request.Scheme](xref:Microsoft.AspNetCore.Http.HttpRequest.Scheme): Set using the `X-Forwarded-Proto` header value.</span></span>
* <span data-ttu-id="8d4cf-393">[HttpContext. Request. Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): `X-Forwarded-Host` üst bilgi değerini kullanarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-393">[HttpContext.Request.Host](xref:Microsoft.AspNetCore.Http.HttpRequest.Host): Set using the `X-Forwarded-Host` header value.</span></span>

<span data-ttu-id="8d4cf-394">İletilen üstbilgiler ara yazılımı [varsayılan ayarları](#forwarded-headers-middleware-options) yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-394">Forwarded Headers Middleware [default settings](#forwarded-headers-middleware-options) can be configured.</span></span> <span data-ttu-id="8d4cf-395">Varsayılan ayarlar şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-395">The default settings are:</span></span>

* <span data-ttu-id="8d4cf-396">Uygulama ve isteklerin kaynağı arasında yalnızca *bir ara sunucu* vardır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-396">There is only *one proxy* between the app and the source of the requests.</span></span>
* <span data-ttu-id="8d4cf-397">Bilinen proxy 'ler ve bilinen ağlar için yalnızca geri döngü adresleri yapılandırılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-397">Only loopback addresses are configured for known proxies and known networks.</span></span>
* <span data-ttu-id="8d4cf-398">İletilen üstbilgiler ve olarak adlandırılır `X-Forwarded-For` `X-Forwarded-Proto` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-398">The forwarded headers are named `X-Forwarded-For` and `X-Forwarded-Proto`.</span></span>

<span data-ttu-id="8d4cf-399">Tüm ağ gereçleri `X-Forwarded-For` `X-Forwarded-Proto` ek yapılandırma olmadan ve üst bilgilerini eklemez.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-399">Not all network appliances add the `X-Forwarded-For` and `X-Forwarded-Proto` headers without additional configuration.</span></span> <span data-ttu-id="8d4cf-400">Proxy istekler uygulamaya ulaştığında bu üst bilgileri içermiyorsa, Gereç üreticinizin kılavuzuna başvurun.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-400">Consult your appliance manufacturer's guidance if proxied requests don't contain these headers when they reach the app.</span></span> <span data-ttu-id="8d4cf-401">Gereç, ve dışındaki farklı üstbilgi adlarını kullanıyorsa `X-Forwarded-For` `X-Forwarded-Proto` , <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini gereç tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-401">If the appliance uses different header names than `X-Forwarded-For` and `X-Forwarded-Proto`, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the appliance.</span></span> <span data-ttu-id="8d4cf-402">Daha fazla bilgi için bkz. [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) ve [farklı üstbilgi adları kullanan bir proxy için yapılandırma](#configuration-for-a-proxy-that-uses-different-header-names).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-402">For more information, see [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) and [Configuration for a proxy that uses different header names](#configuration-for-a-proxy-that-uses-different-header-names).</span></span>

## <a name="iisiis-express-and-aspnet-core-module"></a><span data-ttu-id="8d4cf-403">IIS/IIS Express ve ASP.NET Core modülü</span><span class="sxs-lookup"><span data-stu-id="8d4cf-403">IIS/IIS Express and ASP.NET Core Module</span></span>

<span data-ttu-id="8d4cf-404">İletilen üstbilgiler ara yazılımı, uygulama IIS 'nin arkasında ve ASP.NET Core modülünden sonra [işlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model) barındırılıyorsa [IIS tümleştirme ara yazılımı](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) tarafından varsayılan olarak etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-404">Forwarded Headers Middleware is enabled by default by [IIS Integration Middleware](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when the app is hosted [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind IIS and the ASP.NET Core Module.</span></span> <span data-ttu-id="8d4cf-405">İletilen üstbilgiler ara yazılımı, iletilen üst bilgilerle (örneğin, [IP aldatmacası](https://www.iplocation.net/ip-spoofing)) güven sorunları nedeniyle ASP.NET Core modülüne özgü kısıtlanmış bir yapılandırmaya sahip olan ara yazılım ardışık düzeninde ilk olarak çalışır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-405">Forwarded Headers Middleware is activated to run first in the middleware pipeline with a restricted configuration specific to the ASP.NET Core Module due to trust concerns with forwarded headers (for example, [IP spoofing](https://www.iplocation.net/ip-spoofing)).</span></span> <span data-ttu-id="8d4cf-406">Ara yazılım, `X-Forwarded-For` ve üst bilgilerini iletmek üzere yapılandırılmıştır `X-Forwarded-Proto` ve tek bir localhost proxy ile kısıtlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-406">The middleware is configured to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers and is restricted to a single localhost proxy.</span></span> <span data-ttu-id="8d4cf-407">Ek yapılandırma gerekliyse, [Iletilen üstbilgiler ara yazılım seçeneklerine](#forwarded-headers-middleware-options)bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-407">If additional configuration is required, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options).</span></span>

## <a name="other-proxy-server-and-load-balancer-scenarios"></a><span data-ttu-id="8d4cf-408">Diğer proxy sunucu ve yük dengeleyici senaryoları</span><span class="sxs-lookup"><span data-stu-id="8d4cf-408">Other proxy server and load balancer scenarios</span></span>

<span data-ttu-id="8d4cf-409">[İşlem dışı](xref:host-and-deploy/iis/index#out-of-process-hosting-model)barındırma sırasında [IIS tümleştirmesi](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) 'nin kullanılması dışında, iletilen üstbilgiler ara yazılımı varsayılan olarak etkinleştirilmemiştir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-409">Outside of using [IIS Integration](xref:host-and-deploy/iis/index#enable-the-iisintegration-components) when hosting [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model), Forwarded Headers Middleware isn't enabled by default.</span></span> <span data-ttu-id="8d4cf-410">İletilen üstbilgileri ile işlemek için bir uygulamanın yönlendirilmiş üst bilgi ara yazılımı etkinleştirilmelidir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-410">Forwarded Headers Middleware must be enabled for an app to process forwarded headers with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>.</span></span> <span data-ttu-id="8d4cf-411">Ara yazılım için Hayır belirtilmemişse, ara yazılımı etkinleştirdikten sonra <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> varsayılan [Forwardedheadersoptions. Forwardedheaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) , [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-411">After enabling the middleware if no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified to the middleware, the default [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span>

<span data-ttu-id="8d4cf-412">İle ara yazılımı, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `X-Forwarded-For` içindeki ve üst bilgilerini iletecek şekilde yapılandırın `X-Forwarded-Proto` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-412">Configure the middleware with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> to forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8d4cf-413"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` Diğer ara yazılım çağrılmadan önce metodunu çağırın:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-413">Invoke the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> method in `Startup.Configure` before calling other middleware:</span></span>

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
> <span data-ttu-id="8d4cf-414">Hiçbir <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> `Startup.ConfigureServices` veya doğrudan genişletme yöntemine belirtilmemişse <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> , iletmek Için varsayılan üstbilgiler [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-414">If no <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> are specified in `Startup.ConfigureServices` or directly to the extension method with <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*>, the default headers to forward are [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="8d4cf-415"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders>Özelliğin, iletmek için üst bilgilerle yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-415">The <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> property must be configured with the headers to forward.</span></span>

## <a name="nginx-configuration"></a><span data-ttu-id="8d4cf-416">NGINX yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8d4cf-416">Nginx configuration</span></span>

<span data-ttu-id="8d4cf-417">`X-Forwarded-For`Ve `X-Forwarded-Proto` üst bilgilerini iletmek için bkz <xref:host-and-deploy/linux-nginx#configure-nginx> ..</span><span class="sxs-lookup"><span data-stu-id="8d4cf-417">To forward the `X-Forwarded-For` and `X-Forwarded-Proto` headers, see <xref:host-and-deploy/linux-nginx#configure-nginx>.</span></span> <span data-ttu-id="8d4cf-418">Daha fazla bilgi için bkz. [NGINX: iletilen üstbilgiyi kullanma](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-418">For more information, see [NGINX: Using the Forwarded header](https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/).</span></span>

## <a name="apache-configuration"></a><span data-ttu-id="8d4cf-419">Apache yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8d4cf-419">Apache configuration</span></span>

<span data-ttu-id="8d4cf-420">`X-Forwarded-For`otomatik olarak eklenir (bkz. [Apache modülü mod_proxy: ters proxy Istek üstbilgileri](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-420">`X-Forwarded-For` is added automatically (see [Apache Module mod_proxy: Reverse Proxy Request Headers](https://httpd.apache.org/docs/2.4/mod/mod_proxy.html#x-headers)).</span></span> <span data-ttu-id="8d4cf-421">Üstbilgiyi iletme hakkında daha fazla bilgi için `X-Forwarded-Proto` bkz <xref:host-and-deploy/linux-apache#configure-apache> ..</span><span class="sxs-lookup"><span data-stu-id="8d4cf-421">For information on how to forward the `X-Forwarded-Proto` header, see <xref:host-and-deploy/linux-apache#configure-apache>.</span></span>

## <a name="forwarded-headers-middleware-options"></a><span data-ttu-id="8d4cf-422">İletilen üstbilgiler ara yazılım seçenekleri</span><span class="sxs-lookup"><span data-stu-id="8d4cf-422">Forwarded Headers Middleware options</span></span>

<span data-ttu-id="8d4cf-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions>Iletilen üstbilgiler ara yazılımı davranışını denetleyin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-423"><xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions> control the behavior of the Forwarded Headers Middleware.</span></span> <span data-ttu-id="8d4cf-424">Aşağıdaki örnek varsayılan değerleri değiştirir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-424">The following example changes the default values:</span></span>

* <span data-ttu-id="8d4cf-425">İletilen üst bilgilerdeki giriş sayısını sınırlayın `2` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-425">Limit the number of entries in the forwarded headers to `2`.</span></span>
* <span data-ttu-id="8d4cf-426">Bilinen bir proxy adresi ekleyin `127.0.10.1` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-426">Add a known proxy address of `127.0.10.1`.</span></span>
* <span data-ttu-id="8d4cf-427">İletilen üst bilgi adını varsayılan değerinden olarak değiştirin `X-Forwarded-For` `X-Forwarded-For-My-Custom-Header-Name` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-427">Change the forwarded header name from the default `X-Forwarded-For` to `X-Forwarded-For-My-Custom-Header-Name`.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardLimit = 2;
    options.KnownProxies.Add(IPAddress.Parse("127.0.10.1"));
    options.ForwardedForHeaderName = "X-Forwarded-For-My-Custom-Header-Name";
});
```

| <span data-ttu-id="8d4cf-428">Seçenek</span><span class="sxs-lookup"><span data-stu-id="8d4cf-428">Option</span></span> | <span data-ttu-id="8d4cf-429">Açıklama</span><span class="sxs-lookup"><span data-stu-id="8d4cf-429">Description</span></span> |
| ---
<span data-ttu-id="8d4cf-430">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-430">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-431">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-431">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-432">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-432">'Identity'</span></span>
- <span data-ttu-id="8d4cf-433">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-433">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-434">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-434">'Razor'</span></span>
- <span data-ttu-id="8d4cf-435">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-435">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-436">--- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-436">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-437">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-438">'Identity'</span></span>
- <span data-ttu-id="8d4cf-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-440">'Razor'</span></span>
- <span data-ttu-id="8d4cf-441">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-442">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-442">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-443">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-444">'Identity'</span></span>
- <span data-ttu-id="8d4cf-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-446">'Razor'</span></span>
- <span data-ttu-id="8d4cf-447">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="8d4cf-448">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-448">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="8d4cf-449">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-449">'Blazor'</span></span>
- <span data-ttu-id="8d4cf-450">'Identity'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-450">'Identity'</span></span>
- <span data-ttu-id="8d4cf-451">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-451">'Let's Encrypt'</span></span>
- <span data-ttu-id="8d4cf-452">'Razor'</span><span class="sxs-lookup"><span data-stu-id="8d4cf-452">'Razor'</span></span>
- <span data-ttu-id="8d4cf-453">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-453">'SignalR' uid:</span></span> 

<span data-ttu-id="8d4cf-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Ana bilgisayarları üst bilgiyle `X-Forwarded-Host` belirtilen değerlerle sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-454">------ | | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> | Restricts hosts by the `X-Forwarded-Host` header to the values provided.</span></span><ul><li><span data-ttu-id="8d4cf-455">Değerler, Ordinal-Ignore-Case kullanılarak karşılaştırılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-455">Values are compared using ordinal-ignore-case.</span></span></li><li><span data-ttu-id="8d4cf-456">Bağlantı noktası numaraları dışlanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-456">Port numbers must be excluded.</span></span></li><li><span data-ttu-id="8d4cf-457">Liste boşsa, tüm konaklara izin verilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-457">If the list is empty, all hosts are allowed.</span></span></li><li><span data-ttu-id="8d4cf-458">Üst düzey joker karakter `*` boş olmayan tüm konaklara izin verir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-458">A top-level wildcard `*` allows all non-empty hosts.</span></span></li><li><span data-ttu-id="8d4cf-459">Alt etki alanı Joker karakterlere izin verilir ancak kök etki alanıyla eşleşmez.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-459">Subdomain wildcards are permitted but don't match the root domain.</span></span> <span data-ttu-id="8d4cf-460">Örneğin, `*.contoso.com` `foo.contoso.com` kök etki alanını değil, alt etki alanıyla eşleşir `contoso.com` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-460">For example, `*.contoso.com` matches the subdomain `foo.contoso.com` but not the root domain `contoso.com`.</span></span></li><li><span data-ttu-id="8d4cf-461">Unicode ana bilgisayar adlarına izin verilir, ancak eşleştirme için [puni koduna](https://tools.ietf.org/html/rfc3492) dönüştürülür.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-461">Unicode host names are allowed but are converted to [Punycode](https://tools.ietf.org/html/rfc3492) for matching.</span></span></li><li><span data-ttu-id="8d4cf-462">[IPv6 adresleri](https://tools.ietf.org/html/rfc4291) sınırlayıcı ayraçları içermeli ve [geleneksel biçimde](https://tools.ietf.org/html/rfc4291#section-2.2) olmalıdır (örneğin, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-462">[IPv6 addresses](https://tools.ietf.org/html/rfc4291) must include bounding brackets and be in [conventional form](https://tools.ietf.org/html/rfc4291#section-2.2) (for example, `[ABCD:EF01:2345:6789:ABCD:EF01:2345:6789]`).</span></span> <span data-ttu-id="8d4cf-463">IPv6 adresleri, farklı biçimler arasında mantıksal eşitlik denetimi için özel değildir ve hiçbir kurallı kullanım yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-463">IPv6 addresses aren't special-cased to check for logical equality between different formats, and no canonicalization is performed.</span></span></li><li><span data-ttu-id="8d4cf-464">İzin verilen konaklar kısıtlanamaması, bir saldırganın hizmet tarafından oluşturulan bağlantıları sızdırmasına izin verebilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-464">Failure to restrict the allowed hosts may allow an attacker to spoof links generated by the service.</span></span></li></ul><span data-ttu-id="8d4cf-465">Varsayılan değer boş `IList<string>` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-465">The default value is an empty `IList<string>`.</span></span> <span data-ttu-id="8d4cf-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-466">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedForHeaderName).</span></span> <span data-ttu-id="8d4cf-467">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-For` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-467">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-For` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="8d4cf-468">Varsayılan değer: `X-Forwarded-For`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-468">The default is `X-Forwarded-For`.</span></span> <span data-ttu-id="8d4cf-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Hangi ileticilerin işleneceğini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-469">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders> | Identifies which forwarders should be processed.</span></span> <span data-ttu-id="8d4cf-470">Uygulanan alanların listesi için [Forwardedheaders numaralandırması](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) ' ne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-470">See the [ForwardedHeaders Enum](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders) for the list of fields that apply.</span></span> <span data-ttu-id="8d4cf-471">Bu özelliğe atanan tipik değerler şunlardır `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-471">Typical values assigned to this property are `ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto`.</span></span><br><br><span data-ttu-id="8d4cf-472">Varsayılan değer [Forwardedheaders. None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders)' dır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-472">The default value is [ForwardedHeaders.None](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeaders).</span></span> <span data-ttu-id="8d4cf-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | [Forwardedheadersdefaults varsayılan. XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-473">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedHostHeaderName).</span></span> <span data-ttu-id="8d4cf-474">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Host` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-474">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Host` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="8d4cf-475">Varsayılan değer: `X-Forwarded-Host`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-475">The default is `X-Forwarded-Host`.</span></span> <span data-ttu-id="8d4cf-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-476">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XForwardedProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XForwardedProtoHeaderName).</span></span> <span data-ttu-id="8d4cf-477">Bu seçenek, proxy/iletici üstbilgiyi kullanmıyorsa ve `X-Forwarded-Proto` bilgileri iletmek için başka bir üst bilgi kullandığında kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-477">This option is used when the proxy/forwarder doesn't use the `X-Forwarded-Proto` header but uses some other header to forward the information.</span></span><br><br><span data-ttu-id="8d4cf-478">Varsayılan değer: `X-Forwarded-Proto`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-478">The default is `X-Forwarded-Proto`.</span></span> <span data-ttu-id="8d4cf-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | İşlenen üst bilgilerdeki giriş sayısını sınırlandırır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-479">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardLimit> | Limits the number of entries in the headers that are processed.</span></span> <span data-ttu-id="8d4cf-480">`null`Sınırı devre dışı bırakmak için olarak ayarlayın, ancak bu yalnızca `KnownProxies` veya `KnownNetworks` yapılandırılmışsa yapılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-480">Set to `null` to disable the limit, but this should only be done if `KnownProxies` or `KnownNetworks` are configured.</span></span> <span data-ttu-id="8d4cf-481">Değer olmayan bir `null` önlem, hatalı yapılandırılmış proxy 'lerde ve ağdaki yan kanallardan gelen kötü amaçlı isteklere karşı koruma sağlamak için bir önlem (garanti değil) olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-481">Setting a non-`null` value is a precaution (but not a guarantee) to guard against misconfigured proxies and malicious requests arriving from side-channels on the network.</span></span><br><br><span data-ttu-id="8d4cf-482">İletilen üstbilgiler ara yazılımı, üst bilgileri sağdan sola doğru sırada işler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-482">Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="8d4cf-483">Varsayılan değer ( `1` ) kullanılırsa, değeri artmadığı müddetçe yalnızca üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-483">If the default value (`1`) is used, only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span><br><br><span data-ttu-id="8d4cf-484">Varsayılan değer: `1`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-484">The default is `1`.</span></span> <span data-ttu-id="8d4cf-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | İletilen üstbilgileri kabul etmek için bilinen ağların adres aralıkları.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-485">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownNetworks> | Address ranges of known networks to accept forwarded headers from.</span></span> <span data-ttu-id="8d4cf-486">Sınıfsız Etki alanları arası yönlendirme (CıDR) gösterimini kullanarak IP aralıkları sağlayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-486">Provide IP ranges using Classless Interdomain Routing (CIDR) notation.</span></span><br><br><span data-ttu-id="8d4cf-487">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-487">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="8d4cf-488">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-488">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="8d4cf-489">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-489">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="8d4cf-490">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-490">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="8d4cf-491">Varsayılan, `IList` \<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork> için tek bir giriş içeren bir> `IPAddress.Loopback` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-491">The default is an `IList`\<<xref:Microsoft.AspNetCore.HttpOverrides.IPNetwork>> containing a single entry for `IPAddress.Loopback`.</span></span> <span data-ttu-id="8d4cf-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | İletilen üstbilgileri kabul etmek için bilinen proxy 'lerin adresleri.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-492">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.KnownProxies> | Addresses of known proxies to accept forwarded headers from.</span></span> <span data-ttu-id="8d4cf-493">`KnownProxies`Tam IP adresi eşleşmelerini belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-493">Use `KnownProxies` to specify exact IP address matches.</span></span><br><br><span data-ttu-id="8d4cf-494">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 olarak temsil edilen IPv4 içinde `::ffff:10.0.0.1` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-494">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1`).</span></span> <span data-ttu-id="8d4cf-495">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-495">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="8d4cf-496">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-496">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span> <span data-ttu-id="8d4cf-497">Daha fazla bilgi için, [IPv6 adresi olarak temsil edilen IPv4 adresi Için yapılandırma](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-497">For more information, see the [Configuration for an IPv4 address represented as an IPv6 address](#configuration-for-an-ipv4-address-represented-as-an-ipv6-address) section.</span></span><br><br><span data-ttu-id="8d4cf-498">Varsayılan, `IList` \<<xref:System.Net.IPAddress> için tek bir giriş içeren bir> `IPAddress.IPv6Loopback` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-498">The default is an `IList`\<<xref:System.Net.IPAddress>> containing a single entry for `IPAddress.IPv6Loopback`.</span></span> <span data-ttu-id="8d4cf-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-499">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalForHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalForHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalForHeaderName).</span></span><br><br><span data-ttu-id="8d4cf-500">Varsayılan değer: `X-Original-For`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-500">The default is `X-Original-For`.</span></span> <span data-ttu-id="8d4cf-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | [Forwardedheadersdefaults varsayılan. XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName)tarafından belirtilen yerine bu özellik tarafından belirtilen üstbilgiyi kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-501">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalHostHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalHostHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalHostHeaderName).</span></span><br><br><span data-ttu-id="8d4cf-502">Varsayılan değer: `X-Original-Host`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-502">The default is `X-Original-Host`.</span></span> <span data-ttu-id="8d4cf-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Bu özellik tarafından belirtilen üstbilgiyi, [Forwardedheadersdefaults varsayılan. XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName)tarafından belirtilen bir yerine kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-503">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.OriginalProtoHeaderName> | Use the header specified by this property instead of the one specified by [ForwardedHeadersDefaults.XOriginalProtoHeaderName](xref:Microsoft.AspNetCore.HttpOverrides.ForwardedHeadersDefaults.XOriginalProtoHeaderName).</span></span><br><br><span data-ttu-id="8d4cf-504">Varsayılan değer: `X-Original-Proto`.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-504">The default is `X-Original-Proto`.</span></span> <span data-ttu-id="8d4cf-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Bilgi işlem sayısının, işlenmekte olan [Forwardedheadersoptions. ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) arasında eşitlenmiş olmasını gerektir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-505">| | <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.RequireHeaderSymmetry> | Require the number of header values to be in sync between the [ForwardedHeadersOptions.ForwardedHeaders](xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedHeaders) being processed.</span></span><br><br><span data-ttu-id="8d4cf-506">ASP.NET Core 1. x içindeki varsayılan değer `true` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-506">The default in ASP.NET Core 1.x is `true`.</span></span> <span data-ttu-id="8d4cf-507">ASP.NET Core 2,0 veya sonraki sürümlerde varsayılan değer `false` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-507">The default in ASP.NET Core 2.0 or later is `false`.</span></span> |

## <a name="scenarios-and-use-cases"></a><span data-ttu-id="8d4cf-508">Senaryolar ve kullanım örnekleri</span><span class="sxs-lookup"><span data-stu-id="8d4cf-508">Scenarios and use cases</span></span>

### <a name="when-it-isnt-possible-to-add-forwarded-headers-and-all-requests-are-secure"></a><span data-ttu-id="8d4cf-509">İletilen üstbilgiler eklemek mümkün olmadığında ve tüm istekler güvenlidir</span><span class="sxs-lookup"><span data-stu-id="8d4cf-509">When it isn't possible to add forwarded headers and all requests are secure</span></span>

<span data-ttu-id="8d4cf-510">Bazı durumlarda, iletilen üstbilgileri uygulamaya yönelik isteklere eklemek mümkün olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-510">In some cases, it might not be possible to add forwarded headers to the requests proxied to the app.</span></span> <span data-ttu-id="8d4cf-511">Proxy tüm genel dış isteklerin HTTPS olduğunu zortacaktır, Düzen `Startup.Configure` herhangi bir tür ara yazılım kullanılmadan önce içinde el ile ayarlanabilir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-511">If the proxy is enforcing that all public external requests are HTTPS, the scheme can be manually set in `Startup.Configure` before using any type of middleware:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.Scheme = "https";
    return next();
});
```

<span data-ttu-id="8d4cf-512">Bu kod bir geliştirme veya hazırlama ortamındaki ortam değişkeniyle veya diğer yapılandırma ayarıyla devre dışı bırakılabilir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-512">This code can be disabled with an environment variable or other configuration setting in a development or staging environment.</span></span>

### <a name="deal-with-path-base-and-proxies-that-change-the-request-path"></a><span data-ttu-id="8d4cf-513">Yol tabanı ve istek yolunu değiştiren proxy 'ler ile uğraşın</span><span class="sxs-lookup"><span data-stu-id="8d4cf-513">Deal with path base and proxies that change the request path</span></span>

<span data-ttu-id="8d4cf-514">Bazı proxy 'ler yolu bozulmadan, ancak yönlendirmenin düzgün çalışması için kaldırılması gereken bir uygulama temel yolu ile geçer.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-514">Some proxies pass the path intact but with an app base path that should be removed so that routing works properly.</span></span> <span data-ttu-id="8d4cf-515">[Usepathbaseextensions. usepathbase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) ara yazılımı, yolu HttpRequest. [Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) ve uygulama temeli yoluna, [HttpRequest. pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase)içine böler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-515">[UsePathBaseExtensions.UsePathBase](xref:Microsoft.AspNetCore.Builder.UsePathBaseExtensions.UsePathBase*) middleware splits the path into [HttpRequest.Path](xref:Microsoft.AspNetCore.Http.HttpRequest.Path) and the app base path into [HttpRequest.PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase).</span></span>

<span data-ttu-id="8d4cf-516">, `/foo` Olarak geçirilen bir ara sunucu yolu için uygulama temel yolu ise `/foo/api/1` , ara yazılım `Request.PathBase` `/foo` `Request.Path` `/api/1` aşağıdaki komutla ve olarak ayarlanır:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-516">If `/foo` is the app base path for a proxy path passed as `/foo/api/1`, the middleware sets `Request.PathBase` to `/foo` and `Request.Path` to `/api/1` with the following command:</span></span>

```csharp
app.UsePathBase("/foo");
```

<span data-ttu-id="8d4cf-517">Özgün yol ve yol tabanı, ara yazılım ters ' de çağrıldığında yeniden uygulanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-517">The original path and path base are reapplied when the middleware is called again in reverse.</span></span> <span data-ttu-id="8d4cf-518">Ara yazılım siparişi işleme hakkında daha fazla bilgi için bkz <xref:fundamentals/middleware/index> ..</span><span class="sxs-lookup"><span data-stu-id="8d4cf-518">For more information on middleware order processing, see <xref:fundamentals/middleware/index>.</span></span>

<span data-ttu-id="8d4cf-519">Proxy, yolu kırpar (örneğin, ' `/foo/api/1` ye iletme `/api/1` ), Isteğin [pathbase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) özelliğini ayarlayarak yeniden yönlendirmeleri ve bağlantıları düzeltir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-519">If the proxy trims the path (for example, forwarding `/foo/api/1` to `/api/1`), fix redirects and links by setting the request's [PathBase](xref:Microsoft.AspNetCore.Http.HttpRequest.PathBase) property:</span></span>

```csharp
app.Use((context, next) =>
{
    context.Request.PathBase = new PathString("/foo");
    return next();
});
```

<span data-ttu-id="8d4cf-520">Proxy, yol verileri ekliyor ise, kullanarak yeniden yönlendirmeleri ve bağlantıları onarmak üzere yolun bir bölümünü atın ve özelliğini kullanarak yeniden yönlendirmeyi ve bağlantıları onarın <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> :</span><span class="sxs-lookup"><span data-stu-id="8d4cf-520">If the proxy is adding path data, discard part of the path to fix redirects and links by using <xref:Microsoft.AspNetCore.Http.PathString.StartsWithSegments*> and assigning to the <xref:Microsoft.AspNetCore.Http.HttpRequest.Path> property:</span></span>

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

### <a name="configuration-for-a-proxy-that-uses-different-header-names"></a><span data-ttu-id="8d4cf-521">Farklı üstbilgi adları kullanan bir ara sunucu için yapılandırma</span><span class="sxs-lookup"><span data-stu-id="8d4cf-521">Configuration for a proxy that uses different header names</span></span>

<span data-ttu-id="8d4cf-522">Proxy, adlı üst bilgileri kullanmıyorsa `X-Forwarded-For` ve `X-Forwarded-Proto` proxy adresini/bağlantı noktasını ve kaynak düzen bilgilerini iletmek için, <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> ve <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> seçeneklerini proxy tarafından kullanılan üstbilgi adlarıyla eşleşecek şekilde ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-522">If the proxy doesn't use headers named `X-Forwarded-For` and `X-Forwarded-Proto` to forward the proxy address/port and originating scheme information, set the <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedForHeaderName> and <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.ForwardedProtoHeaderName> options to match the header names used by the proxy:</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.ForwardedForHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-For_Header";
    options.ForwardedProtoHeaderName = "Header_Name_Used_By_Proxy_For_X-Forwarded-Proto_Header";
});
```

### <a name="configuration-for-an-ipv4-address-represented-as-an-ipv6-address"></a><span data-ttu-id="8d4cf-523">IPv6 adresi olarak temsil edilen IPv4 adresinin yapılandırması</span><span class="sxs-lookup"><span data-stu-id="8d4cf-523">Configuration for an IPv4 address represented as an IPv6 address</span></span>

<span data-ttu-id="8d4cf-524">Sunucu çift modlu yuvalar kullanıyorsa, IPv4 adresleri IPv6 biçiminde sağlanır (örneğin, `10.0.0.1` IPv6 'da veya olarak temsil edilen IPv4 `::ffff:10.0.0.1` `::ffff:a00:1` ).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-524">If the server is using dual-mode sockets, IPv4 addresses are supplied in an IPv6 format (for example, `10.0.0.1` in IPv4 represented in IPv6 as `::ffff:10.0.0.1` or `::ffff:a00:1`).</span></span> <span data-ttu-id="8d4cf-525">Bkz. [IPAddress. MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span><span class="sxs-lookup"><span data-stu-id="8d4cf-525">See [IPAddress.MapToIPv6](xref:System.Net.IPAddress.MapToIPv6*).</span></span> <span data-ttu-id="8d4cf-526">[HttpContext. Connection. Remoteıpaddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*)öğesine bakarak bu biçimin gerekip gerekmediğini saptayın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-526">Determine if this format is required by looking at the [HttpContext.Connection.RemoteIpAddress](xref:Microsoft.AspNetCore.Http.ConnectionInfo.RemoteIpAddress*).</span></span>

<span data-ttu-id="8d4cf-527">Aşağıdaki örnekte, iletilen üstbilgiler sağlayan bir ağ adresi `KnownNetworks` listeye IPv6 biçiminde eklenir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-527">In the following example, a network address that supplies forwarded headers is added to the `KnownNetworks` list in IPv6 format.</span></span>

<span data-ttu-id="8d4cf-528">IPv4 adresi:`10.11.12.1/8`</span><span class="sxs-lookup"><span data-stu-id="8d4cf-528">IPv4 address: `10.11.12.1/8`</span></span>

<span data-ttu-id="8d4cf-529">Dönüştürülen IPv6 adresi:`::ffff:10.11.12.1`</span><span class="sxs-lookup"><span data-stu-id="8d4cf-529">Converted IPv6 address: `::ffff:10.11.12.1`</span></span>  
<span data-ttu-id="8d4cf-530">Dönüştürülen önek uzunluğu: 104</span><span class="sxs-lookup"><span data-stu-id="8d4cf-530">Converted prefix length: 104</span></span>

<span data-ttu-id="8d4cf-531">Adresi onaltılık biçimde ( `10.11.12.1` IPv6 olarak temsil edilir) de sağlayabilirsiniz `::ffff:0a0b:0c01` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-531">You can also supply the address in hexadecimal format (`10.11.12.1` represented in IPv6 as `::ffff:0a0b:0c01`).</span></span> <span data-ttu-id="8d4cf-532">Bir IPv4 adresini IPv6 'ya dönüştürürken, `8` ek `::ffff:` IPv6 öneki (8 + 96 = 104) için (ÖRNEKTEKI) CIDR önek uzunluğuna 96 ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-532">When converting an IPv4 address to IPv6, add 96 to the CIDR Prefix Length (`8` in the example) to account for the additional `::ffff:` IPv6 prefix (8 + 96 = 104).</span></span> 

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

## <a name="forward-the-scheme-for-linux-and-non-iis-reverse-proxies"></a><span data-ttu-id="8d4cf-533">Linux ve IIS olmayan ters proxy 'ler için Düzen iletme</span><span class="sxs-lookup"><span data-stu-id="8d4cf-533">Forward the scheme for Linux and non-IIS reverse proxies</span></span>

<span data-ttu-id="8d4cf-534"><xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> Bir azure Linux App Service, Azure Linux sanal MAKINESI (VM) veya IIS 'nin yanı sıra diğer tüm ters proxy 'ler için dağıtıldığında bir siteyi çağıran ve sonsuz döngüye alan uygulamalar.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-534">Apps that call <xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*> and <xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*> put a site into an infinite loop if deployed to an Azure Linux App Service, Azure Linux virtual machine (VM), or behind any other reverse proxy besides IIS.</span></span> <span data-ttu-id="8d4cf-535">TLS, ters proxy tarafından sonlandırılır ve Kestrel doğru istek düzeninden haberdar değildir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-535">TLS is terminated by the reverse proxy, and Kestrel isn't made aware of the correct request scheme.</span></span> <span data-ttu-id="8d4cf-536">OAuth ve OıDC yanlış yeniden yönlendirmeler oluşturduğundan bu yapılandırmada de başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-536">OAuth and OIDC also fail in this configuration because they generate incorrect redirects.</span></span> <span data-ttu-id="8d4cf-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*>IIS 'nin arkasında çalışırken Iletilen üstbilgiler ara yazılımı ekler ve yapılandırır, ancak Linux için eşleşen otomatik yapılandırma (Apache veya NGINX tümleştirmesi) yoktur.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-537"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderIISExtensions.UseIISIntegration*> adds and configures Forwarded Headers Middleware when running behind IIS, but there's no matching automatic configuration for Linux (Apache or Nginx integration).</span></span>

<span data-ttu-id="8d4cf-538">IIS olmayan senaryolarda düzeni proxy 'den iletmek için, Iletilen üstbilgiler ara yazılımını ekleyin ve yapılandırın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-538">To forward the scheme from the proxy in non-IIS scenarios, add and configure Forwarded Headers Middleware.</span></span> <span data-ttu-id="8d4cf-539">İçinde `Startup.ConfigureServices` , aşağıdaki kodu kullanın:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-539">In `Startup.ConfigureServices`, use the following code:</span></span>

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

## <a name="troubleshoot"></a><span data-ttu-id="8d4cf-540">Sorun giderme</span><span class="sxs-lookup"><span data-stu-id="8d4cf-540">Troubleshoot</span></span>

<span data-ttu-id="8d4cf-541">Üstbilgiler beklendiği gibi iletilemediği zaman [günlüğe kaydetmeyi](xref:fundamentals/logging/index)etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-541">When headers aren't forwarded as expected, enable [logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="8d4cf-542">Günlükler sorunu gidermek için yeterli bilgi sağlamıyorsa, sunucu tarafından alınan istek üstbilgilerini numaralandırın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-542">If the logs don't provide sufficient information to troubleshoot the problem, enumerate the request headers received by the server.</span></span> <span data-ttu-id="8d4cf-543">Bir uygulama yanıtına istek üst bilgileri yazmak veya üst bilgileri günlüğe kaydetmek için satır içi ara yazılım kullanın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-543">Use inline middleware to write request headers to an app response or log the headers.</span></span> 

<span data-ttu-id="8d4cf-544">Üst bilgileri uygulamanın yanıtına yazmak için, aşağıdaki Terminal satır içi ara yazılımını, içindeki çağrısından hemen sonra yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="8d4cf-544">To write the headers to the app's response, place the following terminal inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`:</span></span>

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

<span data-ttu-id="8d4cf-545">Yanıt gövdesi yerine günlüklere yazabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-545">You can write to logs instead of the response body.</span></span> <span data-ttu-id="8d4cf-546">Günlüklere yazmak, sitenin hata ayıklanırken normal şekilde çalışmasına olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-546">Writing to logs allows the site to function normally while debugging.</span></span>

<span data-ttu-id="8d4cf-547">Yanıt gövdesi yerine günlükleri yazmak için:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-547">To write logs rather than to the response body:</span></span>

* <span data-ttu-id="8d4cf-548">`ILogger<Startup>` `Startup` [Başlangıçta günlükleri oluşturma](xref:fundamentals/logging/index#create-logs-in-startup)bölümünde açıklandığı gibi sınıfına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-548">Inject `ILogger<Startup>` into the `Startup` class as described in [Create logs in Startup](xref:fundamentals/logging/index#create-logs-in-startup).</span></span>
* <span data-ttu-id="8d4cf-549">İçindeki çağrısından hemen sonra aşağıdaki satır içi ara yazılımı yerleştirin <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-549">Place the following inline middleware immediately after the call to <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersExtensions.UseForwardedHeaders*> in `Startup.Configure`.</span></span>

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

<span data-ttu-id="8d4cf-550">İşlendiğinde, `X-Forwarded-{For|Proto|Host}` değerler öğesine taşınır `X-Original-{For|Proto|Host}` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-550">When processed, `X-Forwarded-{For|Proto|Host}` values are moved to `X-Original-{For|Proto|Host}`.</span></span> <span data-ttu-id="8d4cf-551">Belirli bir üst bilgide birden çok değer varsa, Iletilen üstbilgiler ara yazılımı sağdan sola doğru sırada üst bilgileri işler.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-551">If there are multiple values in a given header, Forwarded Headers Middleware processes headers in reverse order from right to left.</span></span> <span data-ttu-id="8d4cf-552">Varsayılan değer `ForwardLimit` `1` (bir) ise, değeri arttırılmadığı müddetçe yalnızca en sağdaki üst bilgilerden en sağdaki değer işlenir `ForwardLimit` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-552">The default `ForwardLimit` is `1` (one), so only the rightmost value from the headers is processed unless the value of `ForwardLimit` is increased.</span></span>

<span data-ttu-id="8d4cf-553">`KnownProxies`İletilen üstbilgiler işlenmeden önce isteğin özgün uzak IP 'si veya listelerindeki bir girdiyle eşleşmesi gerekir `KnownNetworks` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-553">The request's original remote IP must match an entry in the `KnownProxies` or `KnownNetworks` lists before forwarded headers are processed.</span></span> <span data-ttu-id="8d4cf-554">Bu, güvenilmeyen proxy 'lerden ileticileri kabul etmediği için üst bilgi yanıltmasını kısıtlar.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-554">This limits header spoofing by not accepting forwarders from untrusted proxies.</span></span> <span data-ttu-id="8d4cf-555">Bilinmeyen bir proxy algılandığında günlüğe kaydetme, proxy 'nin adresini gösterir:</span><span class="sxs-lookup"><span data-stu-id="8d4cf-555">When an unknown proxy is detected, logging indicates the address of the proxy:</span></span>

```console
September 20th 2018, 15:49:44.168 Unknown proxy: 10.0.0.100:54321
```

<span data-ttu-id="8d4cf-556">Yukarıdaki örnekte, alana 10.0.0.100 bir proxy sunucusudur.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-556">In the preceding example, 10.0.0.100 is a proxy server.</span></span> <span data-ttu-id="8d4cf-557">Sunucu, güvenilir bir ara sunucu ise, sunucusunun IP adresini `KnownProxies` ' e (veya güvenilen bir ağı öğesine ekleyin) ekleyin `KnownNetworks` `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="8d4cf-557">If the server is a trusted proxy, add the server's IP address to `KnownProxies` (or add a trusted network to `KnownNetworks`) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="8d4cf-558">Daha fazla bilgi için [Iletilen üstbilgiler ara yazılım seçenekleri](#forwarded-headers-middleware-options) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-558">For more information, see the [Forwarded Headers Middleware options](#forwarded-headers-middleware-options) section.</span></span>

```csharp
services.Configure<ForwardedHeadersOptions>(options =>
{
    options.KnownProxies.Add(IPAddress.Parse("10.0.0.100"));
});
```

> [!IMPORTANT]
> <span data-ttu-id="8d4cf-559">Yalnızca güvenilen proxy 'lerin ve ağların üstbilgileri iletmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-559">Only allow trusted proxies and networks to forward headers.</span></span> <span data-ttu-id="8d4cf-560">Aksi takdirde, [IP sahtekarlığı](https://www.iplocation.net/ip-spoofing) saldırıları mümkündür.</span><span class="sxs-lookup"><span data-stu-id="8d4cf-560">Otherwise, [IP spoofing](https://www.iplocation.net/ip-spoofing) attacks are possible.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8d4cf-561">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8d4cf-561">Additional resources</span></span>

* <xref:host-and-deploy/web-farm>
* [<span data-ttu-id="8d4cf-562">Microsoft güvenlik danışmanlık CVE-2018-0787: ayrıcalık yükselmesi güvenlik açığı ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="8d4cf-562">Microsoft Security Advisory CVE-2018-0787: ASP.NET Core Elevation Of Privilege Vulnerability</span></span>](https://github.com/aspnet/Announcements/issues/295)

::: moniker-end
