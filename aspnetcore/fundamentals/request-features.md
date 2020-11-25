---
title: ASP.NET Core içindeki istek özellikleri
author: ardalis
description: ASP.NET Core arabirimlerde tanımlanan HTTP istekleri ve yanıtları ile ilgili Web sunucusu uygulama ayrıntıları hakkında bilgi edinin.
ms.author: riande
ms.custom: mvc
ms.date: 10/20/2020
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
uid: fundamentals/request-features
ms.openlocfilehash: 88e97d88341789a76a79da8d92098c2e00396fe7
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870431"
---
# <a name="request-features-in-aspnet-core"></a><span data-ttu-id="cbbaf-103">ASP.NET Core içindeki istek özellikleri</span><span class="sxs-lookup"><span data-stu-id="cbbaf-103">Request Features in ASP.NET Core</span></span>

<span data-ttu-id="cbbaf-104">[Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="cbbaf-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cbbaf-105">`HttpContext`Uygulamaların ve ara yazılımların istekleri işlemek için kullandığı API 'nin altında, *özellik arabirimleri* olarak adlandırılan bir soyutlama katmanı vardır.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-105">The `HttpContext` API that applications and middleware use to process requests has an abstraction layer underneath it called *feature interfaces*.</span></span> <span data-ttu-id="cbbaf-106">Her özellik arabirimi tarafından sunulan işlevlerin ayrıntılı bir alt kümesini sağlar `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="cbbaf-106">Each feature interface provides a granular subset of the functionality exposed by `HttpContext`.</span></span> <span data-ttu-id="cbbaf-107">Bu arabirimler, isteğin tamamı yeniden uygulanması gerekmeden işlenirken, sunucu veya ara yazılım tarafından eklenebilir, değiştirilebilir, sarmalanabilir, değiştirilebilir veya kaldırılabilir `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="cbbaf-107">These interfaces can be added, modified, wrapped, replaced, or even removed by the server or middleware as the request is processed without having to re-implement the entire `HttpContext`.</span></span> <span data-ttu-id="cbbaf-108">Ayrıca, test sırasında işlevselliği sahte bir şekilde kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-108">They can also be used to mock functionality when testing.</span></span>

## <a name="feature-collections"></a><span data-ttu-id="cbbaf-109">Özellik koleksiyonları</span><span class="sxs-lookup"><span data-stu-id="cbbaf-109">Feature collections</span></span>

<span data-ttu-id="cbbaf-110"><xref:Microsoft.AspNetCore.Http.HttpContext.Features>Özelliği, `HttpContext` geçerli istek için özellik arabirimleri koleksiyonuna erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-110">The <xref:Microsoft.AspNetCore.Http.HttpContext.Features> property of `HttpContext` provides access to the collection of feature interfaces for the current request.</span></span> <span data-ttu-id="cbbaf-111">Özellik koleksiyonu bir istek bağlamı içinde bile değişebilir olduğundan, ara yazılım, koleksiyonu değiştirmek ve ek özellikler için destek eklemek üzere kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-111">Since the feature collection is mutable even within the context of a request, middleware can be used to modify the collection and add support for additional features.</span></span> <span data-ttu-id="cbbaf-112">Bazı gelişmiş özellikler yalnızca özellik koleksiyonu aracılığıyla ilişkili arabirime erişerek kullanılabilir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-112">Some advanced features are only available by accessing the associated interface through the feature collection.</span></span>

## <a name="feature-interfaces"></a><span data-ttu-id="cbbaf-113">Özellik arabirimleri</span><span class="sxs-lookup"><span data-stu-id="cbbaf-113">Feature interfaces</span></span>

<span data-ttu-id="cbbaf-114">ASP.NET Core, ' de <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> destekledikleri özellikleri belirlemek için çeşitli sunucular ve ara yazılım tarafından paylaşılan bir dizi ortak http özelliği arabirimini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-114">ASP.NET Core defines a number of common HTTP feature interfaces in <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>, which are shared by various servers and middleware to identify the features that they support.</span></span> <span data-ttu-id="cbbaf-115">Sunucular ve ara yazılım, ek işlevlere sahip kendi arabirimlerini de sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-115">Servers and middleware may also provide their own interfaces with additional functionality.</span></span>

<span data-ttu-id="cbbaf-116">Çoğu özellik arabirimi isteğe bağlı, açık işlevsellik sağlar ve ilgili `HttpContext` API 'leri, özellik mevcut değilse varsayılan değerleri sağlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-116">Most feature interfaces provide optional, light-up functionality, and their associated `HttpContext` APIs provide defaults if the feature isn't present.</span></span> <span data-ttu-id="cbbaf-117">Çekirdek istek ve yanıt işlevselliği sağladığından ve isteği işlemek için uygulanması gerektiğinden, aşağıdaki içerikte bazı arabirimler belirtilmiştir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-117">A few interfaces are indicated in the following content as required because they provide core request and response functionality and must be implemented in order to process the request.</span></span>

<span data-ttu-id="cbbaf-118">Aşağıdaki özellik arabirimleri şunlardır <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName> :</span><span class="sxs-lookup"><span data-stu-id="cbbaf-118">The following feature interfaces are from <xref:Microsoft.AspNetCore.Http.Features?displayProperty=fullName>:</span></span>

<span data-ttu-id="cbbaf-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Protokol, yol, sorgu dizesi, üst bilgiler ve gövde dahil olmak üzere bir HTTP isteğinin yapısını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-119"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestFeature>: Defines the structure of an HTTP request, including the protocol, path, query string, headers, and body.</span></span> <span data-ttu-id="cbbaf-120">İstekleri işlemek için bu özellik gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-120">This feature is required in order to process requests.</span></span>

<span data-ttu-id="cbbaf-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Durum kodu, üst bilgiler ve yanıtın gövdesi dahil olmak üzere bir HTTP yanıtının yapısını tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-121"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseFeature>: Defines the structure of an HTTP response, including the status code, headers, and body of the response.</span></span> <span data-ttu-id="cbbaf-122">İstekleri işlemek için bu özellik gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-122">This feature is required in order to process requests.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cbbaf-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Bir `Stream` , a veya bir dosya kullanarak yanıt gövdesini yazmanın farklı yollarını tanımlar `PipeWriter` .</span><span class="sxs-lookup"><span data-stu-id="cbbaf-123"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseBodyFeature>: Defines different ways of writing out the response body, using either a `Stream`, a `PipeWriter`, or a file.</span></span> <span data-ttu-id="cbbaf-124">İstekleri işlemek için bu özellik gereklidir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-124">This feature is required in order to process requests.</span></span> <span data-ttu-id="cbbaf-125">Bu ve ' nin yerini alır `IHttpResponseFeature.Body` `IHttpSendFileFeature` .</span><span class="sxs-lookup"><span data-stu-id="cbbaf-125">This replaces `IHttpResponseFeature.Body` and `IHttpSendFileFeature`.</span></span>

::: moniker-end

<span data-ttu-id="cbbaf-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: <xref:System.Security.Claims.ClaimsPrincipal> Şu anda istekle ilişkili olan öğesini tutar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-126"><xref:Microsoft.AspNetCore.Http.Features.Authentication.IHttpAuthenticationFeature>: Holds the <xref:System.Security.Claims.ClaimsPrincipal> currently associated with the request.</span></span>

<span data-ttu-id="cbbaf-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Gelen HTTP ve çok parçalı form gönderilerini ayrıştırmak ve önbelleğe almak için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-127"><xref:Microsoft.AspNetCore.Http.Features.IFormFeature>: Used to parse and cache incoming HTTP and multipart form submissions.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cbbaf-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: İstek veya Yanıt gövdeleri için zaman uyumlu GÇ işlemlerine izin verilip verilmeyeceğini denetlemek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-128"><xref:Microsoft.AspNetCore.Http.Features.IHttpBodyControlFeature>: Used to control if synchronous IO operations are allowed for the request or response bodies.</span></span>

::: moniker-end
   
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cbbaf-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: İsteklerin ve/veya yanıtlarının arabelleğe alınması devre dışı bırakmak için yöntemleri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-129"><xref:Microsoft.AspNetCore.Http.Features.IHttpBufferingFeature>: Defines methods for disabling buffering of requests and/or responses.</span></span>

::: moniker-end

<span data-ttu-id="cbbaf-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Bağlantı kimliği ve yerel ve uzak adresler ve bağlantı noktaları için özellikleri tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-130"><xref:Microsoft.AspNetCore.Http.Features.IHttpConnectionFeature>: Defines properties for the connection id and local and remote addresses and ports.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="cbbaf-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Geçerli istek için izin verilen en fazla istek gövdesi boyutunu denetler.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-131"><xref:Microsoft.AspNetCore.Http.Features.IHttpMaxRequestBodySizeFeature>: Controls the maximum allowed request body size for the current request.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="cbbaf-132">`IHttpRequestBodyDetectionFeature`: İsteğin bir gövdeye sahip olup olmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-132">`IHttpRequestBodyDetectionFeature`: Indicates if the request can have a body.</span></span>

::: moniker-end

<span data-ttu-id="cbbaf-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: İstekleri benzersiz şekilde tanımlamak için uygulanabilecek bir özellik ekler.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-133"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestIdentifierFeature>: Adds a property that can be implemented to uniquely identify requests.</span></span>

<span data-ttu-id="cbbaf-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Bağlantı iptal etme veya bir isteğin erken sonlandırılıp sonlandırılmayacağını (örneğin, bir istemcinin bağlantısının kesilmesi) için desteği tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-134"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestLifetimeFeature>: Defines support for aborting connections or detecting if a request has been terminated prematurely, such as by a client disconnect.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cbbaf-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Varsa istek artbilgisi üst bilgilerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-135"><xref:Microsoft.AspNetCore.Http.Features.IHttpRequestTrailersFeature>: Provides access to the request trailer headers, if any.</span></span>

<span data-ttu-id="cbbaf-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: HTTP/2 veya HTTP/3 gibi bunları destekleyen protokoller için sıfırlama iletileri göndermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-136"><xref:Microsoft.AspNetCore.Http.Features.IHttpResetFeature>: Used to send reset messages for protocols that support them such as HTTP/2 or HTTP/3.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="cbbaf-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Destekleniyorsa yanıt artbilgisi bilgilerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-137"><xref:Microsoft.AspNetCore.Http.Features.IHttpResponseTrailersFeature>: Enables the application to provide response trailer headers if supported.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cbbaf-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Dosyaları zaman uyumsuz olarak göndermek için bir yöntem tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-138"><xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>: Defines a method for sending files asynchronously.</span></span>

::: moniker-end

<span data-ttu-id="cbbaf-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Sunucu protokolleri değiştirmek isterse, istemcinin kullanmak istediğiniz ek protokolleri belirtmesini sağlayan [http yükseltmeleri](https://tools.ietf.org/html/rfc2616.html#section-14.42)için desteği tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-139"><xref:Microsoft.AspNetCore.Http.Features.IHttpUpgradeFeature>: Defines support for [HTTP Upgrades](https://tools.ietf.org/html/rfc2616.html#section-14.42), which allow the client to specify which additional protocols it would like to use if the server wishes to switch protocols.</span></span>

<span data-ttu-id="cbbaf-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Web yuvalarını desteklemek için bir API tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-140"><xref:Microsoft.AspNetCore.Http.Features.IHttpWebSocketFeature>: Defines an API for supporting web sockets.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cbbaf-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Yanıt sıkıştırmasının HTTPS bağlantıları üzerinden kullanılması gerekip gerekmediğini denetler.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-141"><xref:Microsoft.AspNetCore.Http.Features.IHttpsCompressionFeature>: Controls if response compression should be used over HTTPS connections.</span></span>

::: moniker-end

<span data-ttu-id="cbbaf-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> İstek başına uygulama durumu için koleksiyonu depolar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-142"><xref:Microsoft.AspNetCore.Http.Features.IItemsFeature>: Stores the <xref:Microsoft.AspNetCore.Http.Features.IItemsFeature.Items> collection for per request application state.</span></span>

<span data-ttu-id="cbbaf-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Sorgu dizesini ayrıştırır ve önbelleğe alır.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-143"><xref:Microsoft.AspNetCore.Http.Features.IQueryFeature>: Parses and caches the query string.</span></span>
   
::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cbbaf-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: İstek gövdesini bir olarak temsil eder <xref:System.IO.Pipelines.PipeReader> .</span><span class="sxs-lookup"><span data-stu-id="cbbaf-144"><xref:Microsoft.AspNetCore.Http.Features.IRequestBodyPipeFeature>: Represents the request body as a <xref:System.IO.Pipelines.PipeReader>.</span></span>
 
::: moniker-end

<span data-ttu-id="cbbaf-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: İstek üst bilgisi değerlerini ayrıştırır ve önbelleğe alır `Cookie` .</span><span class="sxs-lookup"><span data-stu-id="cbbaf-145"><xref:Microsoft.AspNetCore.Http.Features.IRequestCookiesFeature>: Parses and caches the request `Cookie` header values.</span></span>

<span data-ttu-id="cbbaf-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Yanıtın üstbilgiye nasıl cookie uygulanacağını denetler `Set-Cookie` .</span><span class="sxs-lookup"><span data-stu-id="cbbaf-146"><xref:Microsoft.AspNetCore.Http.Features.IResponseCookiesFeature>: Controls how response cookies are applied to the `Set-Cookie` header.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="cbbaf-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: Bu özellik, IIS tarafından sağlananlar gibi istek sunucusu değişkenlerine erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-147"><xref:Microsoft.AspNetCore.Http.Features.IServerVariablesFeature>: This feature provides access to request server variables such as those provided by IIS.</span></span>

::: moniker-end
   
<span data-ttu-id="cbbaf-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: <xref:System.IServiceProvider> Kapsamlı bir istek hizmetleriyle erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-148"><xref:Microsoft.AspNetCore.Http.Features.IServiceProvidersFeature>: Provides access to an <xref:System.IServiceProvider> with scoped request services.</span></span>

<span data-ttu-id="cbbaf-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: `ISessionFactory` <xref:Microsoft.AspNetCore.Http.ISession> Kullanıcı oturumlarını desteklemek için tanımlar ve soyutlamalar tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-149"><xref:Microsoft.AspNetCore.Http.Features.ISessionFeature>: Defines `ISessionFactory` and <xref:Microsoft.AspNetCore.Http.ISession> abstractions for supporting user sessions.</span></span> <span data-ttu-id="cbbaf-150">`ISessionFeature` , <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (bkz <xref:fundamentals/app-state> .) tarafından uygulanır.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-150">`ISessionFeature` is implemented by the <xref:Microsoft.AspNetCore.Session.SessionMiddleware> (see <xref:fundamentals/app-state>).</span></span>

<span data-ttu-id="cbbaf-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: İstemci sertifikalarını almak için bir API tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-151"><xref:Microsoft.AspNetCore.Http.Features.ITlsConnectionFeature>: Defines an API for retrieving client certificates.</span></span>

<span data-ttu-id="cbbaf-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: TLS belirteci bağlama parametreleriyle çalışma yöntemlerini tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-152"><xref:Microsoft.AspNetCore.Http.Features.ITlsTokenBindingFeature>: Defines methods for working with TLS token binding parameters.</span></span>
   
::: moniker range=">= aspnetcore-2.2"
   
<span data-ttu-id="cbbaf-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Site etkinliğiyle ve işlevlerle ilgili Kullanıcı bilgilerinin depolanması ile ilgili Kullanıcı onayını sorgulamak, vermek ve geri çekmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cbbaf-153"><xref:Microsoft.AspNetCore.Http.Features.ITrackingConsentFeature>: Used to query, grant, and withdraw user consent regarding the storage of user information related to site activity and functionality.</span></span>
   
::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cbbaf-154">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cbbaf-154">Additional resources</span></span>

* <xref:fundamentals/servers/index>
* <xref:fundamentals/middleware/index>
