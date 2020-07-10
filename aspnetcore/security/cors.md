---
title: ASP.NET Core 'de çıkış noktaları arası Istekleri (CORS) etkinleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında çapraz kaynak isteklerini izin vermek veya reddetmek için CORS 'nin nasıl standart olduğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: dc8e68ef482025443147eeb27bd3f245c1a1a5ed
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212889"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="b8912-103">ASP.NET Core 'de çıkış noktaları arası Istekleri (CORS) etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="b8912-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b8912-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b8912-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="b8912-105">Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b8912-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="b8912-106">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="b8912-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="b8912-107">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b8912-108">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="b8912-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b8912-109">Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b8912-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="b8912-110">Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="b8912-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="b8912-111">[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="b8912-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="b8912-112">, Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="b8912-113">Güvenlik özelliği **değil** , CORS güvenliği.</span><span class="sxs-lookup"><span data-stu-id="b8912-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="b8912-114">CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="b8912-115">Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="b8912-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="b8912-116">Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="b8912-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="b8912-117">, [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="b8912-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="b8912-118">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8912-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="b8912-119">Aynı kaynak</span><span class="sxs-lookup"><span data-stu-id="b8912-119">Same origin</span></span>

<span data-ttu-id="b8912-120">Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="b8912-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="b8912-121">Bu iki URL aynı kaynağa sahiptir:</span><span class="sxs-lookup"><span data-stu-id="b8912-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="b8912-122">Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:</span><span class="sxs-lookup"><span data-stu-id="b8912-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="b8912-123">`https://example.net`: Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="b8912-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="b8912-124">`https://www.example.com/foo.html`: Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="b8912-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="b8912-125">`http://example.com/foo.html`: Farklı düzen</span><span class="sxs-lookup"><span data-stu-id="b8912-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="b8912-126">`https://example.com:9000/foo.html`: Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="b8912-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="b8912-127">CORS'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="b8912-127">Enable CORS</span></span>

<span data-ttu-id="b8912-128">CORS 'yi etkinleştirmenin üç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="b8912-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="b8912-129">[Adlandırılmış bir ilke](#np) veya [varsayılan ilke](#dp)kullanan ara yazılımlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="b8912-130">[Uç nokta yönlendirme](#ecors)kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="b8912-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="b8912-131">[[Enablecors]](#attr) özniteliğiyle birlikte.</span><span class="sxs-lookup"><span data-stu-id="b8912-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="b8912-132">Adlandırılmış ilkeyle [[Enablecors]](#attr) ÖZNITELIĞINI kullanmak CORS 'yi destekleyen uç noktaları sınırlayan en yoğun denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

> [!WARNING]
> <span data-ttu-id="b8912-133"><xref:Owin.CorsExtensions.UseCors%2A>, kullanılmadan önce çağrılmalıdır <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> `UseResponseCaching` .</span><span class="sxs-lookup"><span data-stu-id="b8912-133"><xref:Owin.CorsExtensions.UseCors%2A> must be called before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> when using `UseResponseCaching`.</span></span>

<span data-ttu-id="b8912-134">Her yaklaşım aşağıdaki bölümlerde ayrıntılı olarak verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b8912-134">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="b8912-135">Adlandırılmış ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-135">CORS with named policy and middleware</span></span>

<span data-ttu-id="b8912-136">CORS ara yazılımı, çıkış noktaları arası istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="b8912-136">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="b8912-137">Aşağıdaki kod, belirtilen kaynakları kullanarak tüm uygulamanın uç noktalarına bir CORS ilkesi uygular:</span><span class="sxs-lookup"><span data-stu-id="b8912-137">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,32)]

<span data-ttu-id="b8912-138">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="b8912-138">The preceding code:</span></span>

* <span data-ttu-id="b8912-139">İlke adını olarak ayarlar `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="b8912-139">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="b8912-140">İlke adı rastgele olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-140">The policy name is arbitrary.</span></span>
* <span data-ttu-id="b8912-141"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>Uzantı yöntemini çağırır ve `_myAllowSpecificOrigins` CORS ilkesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="b8912-141">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="b8912-142">`UseCors`CORS ara yazılımını ekler.</span><span class="sxs-lookup"><span data-stu-id="b8912-142">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="b8912-143">Çağrısının `UseCors` sonra `UseRouting` , öncesi yerleştirilmesi gerekir `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="b8912-143">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="b8912-144">Daha fazla bilgi için bkz. [Ara yazılım sırası](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="b8912-144">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="b8912-145"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar.</span><span class="sxs-lookup"><span data-stu-id="b8912-145">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b8912-146">Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b8912-146">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="b8912-147">Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b8912-147">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="b8912-148">`_myAllowSpecificOrigins`Tüm denetleyici uç noktaları için CORS ilkesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-148">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="b8912-149">Belirli uç noktalara bir CORS ilkesi uygulamak için bkz. [Endpoint Routing](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="b8912-149">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>
* <span data-ttu-id="b8912-150">[Yanıt önbelleğe alma ara yazılımını](xref:performance/caching/middleware)kullanırken, daha <xref:Owin.CorsExtensions.UseCors%2A> önce çağırın <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A> .</span><span class="sxs-lookup"><span data-stu-id="b8912-150">When using [Response Caching Middleware](xref:performance/caching/middleware), call <xref:Owin.CorsExtensions.UseCors%2A> before <xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching%2A>.</span></span>

<span data-ttu-id="b8912-151">Endpoint Routing ile **CORS ara yazılımı** , ve çağrıları arasında yürütülecek şekilde yapılandırılmalıdır `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="b8912-151">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="b8912-152">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="b8912-152">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="b8912-153"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:</span><span class="sxs-lookup"><span data-stu-id="b8912-153">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="b8912-154">Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b8912-154">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="b8912-155"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemler aşağıdaki kodda gösterildiği gibi zincirleme olabilir:</span><span class="sxs-lookup"><span data-stu-id="b8912-155">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="b8912-156">Not: belirtilen URL sonunda eğik **not** çizgi ( `/` ) içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="b8912-156">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="b8912-157">URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="b8912-157">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="b8912-158">Varsayılan ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-158">CORS with default policy and middleware</span></span>

<span data-ttu-id="b8912-159">Aşağıdaki vurgulanan kod varsayılan CORS ilkesini sunar:</span><span class="sxs-lookup"><span data-stu-id="b8912-159">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="b8912-160">Yukarıdaki kod, tüm denetleyici uç noktalarına varsayılan CORS ilkesini uygular.</span><span class="sxs-lookup"><span data-stu-id="b8912-160">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="b8912-161">Uç nokta yönlendirme ile CORS 'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="b8912-161">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="b8912-162">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** .</span><span class="sxs-lookup"><span data-stu-id="b8912-162">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="b8912-163">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/20709) bakın ve [uç nokta yönlendirme ve [HttpOptions] ile test CORS 'yi test](#tcer)edin.</span><span class="sxs-lookup"><span data-stu-id="b8912-163">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="b8912-164">Uç nokta yönlendirmesinde, CORS uzantı yöntemleri kümesi kullanılarak uç nokta temelinde etkinleştirilebilir <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-164">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="b8912-165">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="b8912-165">In the preceding code:</span></span>

* <span data-ttu-id="b8912-166">`app.UseCors`CORS ara yazılımını sunar.</span><span class="sxs-lookup"><span data-stu-id="b8912-166">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="b8912-167">Varsayılan bir ilke yapılandırılmadığından, `app.UseCors()` tek BAŞıNA CORS 'yi etkinleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="b8912-167">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="b8912-168">`/echo`Ve denetleyici uç noktaları, belirtilen ilkeyi kullanarak çıkış noktaları arası isteklere izin verir.</span><span class="sxs-lookup"><span data-stu-id="b8912-168">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="b8912-169">`/echo2` Razor Varsayılan ilke belirtilmediğinden ve sayfa uç noktaları, çıkış noktaları arası isteklere izin **vermez** .</span><span class="sxs-lookup"><span data-stu-id="b8912-169">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="b8912-170">[[Disablecors]](#dc) özniteliği, ile Endpoint Routing tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="b8912-170">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="b8912-171">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="b8912-171">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="b8912-172">CORS 'yi özniteliklerle etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b8912-172">Enable CORS with attributes</span></span>

<span data-ttu-id="b8912-173">CORS 'yi [[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliğiyle etkinleştirmek ve yalnızca CORS gerektiren uç noktalara adlandırılmış bir ilke uygulamak, en derin denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-173">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="b8912-174">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) ÖZNITELIĞI, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-174">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="b8912-175">`[EnableCors]`Öznitelik, tüm uç noktalar yerine seçili uç noktalar IÇIN CORS 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="b8912-175">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="b8912-176">`[EnableCors]`varsayılan ilkeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="b8912-176">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="b8912-177">`[EnableCors("{Policy String}")]`adlandırılmış bir ilke belirtir.</span><span class="sxs-lookup"><span data-stu-id="b8912-177">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="b8912-178">`[EnableCors]`Özniteliği öğesine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="b8912-178">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="b8912-179">Sayfasında`PageModel`</span><span class="sxs-lookup"><span data-stu-id="b8912-179"> Page `PageModel`</span></span>
* <span data-ttu-id="b8912-180">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="b8912-180">Controller</span></span>
* <span data-ttu-id="b8912-181">Denetleyici eylemi yöntemi</span><span class="sxs-lookup"><span data-stu-id="b8912-181">Controller action method</span></span>

<span data-ttu-id="b8912-182">Öznitelikleri olan denetleyicilere, sayfa modellerine veya eylem yöntemlerine farklı ilkeler uygulanabilir `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="b8912-182">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="b8912-183">`[EnableCors]`Öznitelik bir denetleyiciye, sayfa modeline veya eylem yöntemine uygulandığında ve bir ara yazılım IÇINDE CORS etkinleştirildiğinde, **her iki ilke de** uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b8912-183">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="b8912-184">\*\*İlkelerin birleştirilmesi önerilir. \*\* `[EnableCors]` **Aynı uygulamada değil, özniteliğini veya ara yazılımını kullanın.**</span><span class="sxs-lookup"><span data-stu-id="b8912-184">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="b8912-185">Aşağıdaki kod her bir yönteme farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="b8912-185">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="b8912-186">Aşağıdaki kod iki CORS ilkesi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b8912-186">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="b8912-187">CORS isteklerini sınırlayan en yoğun denetim için:</span><span class="sxs-lookup"><span data-stu-id="b8912-187">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="b8912-188">`[EnableCors("MyPolicy")]`Adlandırılmış ilkeyle kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8912-188">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="b8912-189">Varsayılan bir ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="b8912-189">Don't define a default policy.</span></span>
* <span data-ttu-id="b8912-190">[Endpoint Routing](#ecors)kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="b8912-190">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="b8912-191">Sonraki bölümdeki kod, önceki listeyi karşılar.</span><span class="sxs-lookup"><span data-stu-id="b8912-191">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="b8912-192">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="b8912-192">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="b8912-193">CORS 'yi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b8912-193">Disable CORS</span></span>

<span data-ttu-id="b8912-194">[[Disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği [Endpoint ROUTING](#ecors)tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** .</span><span class="sxs-lookup"><span data-stu-id="b8912-194">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="b8912-195">Aşağıdaki kod CORS ilkesini tanımlar `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="b8912-195">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="b8912-196">Aşağıdaki kod, eylem için CORS 'yi devre dışı bırakır `GetValues2` :</span><span class="sxs-lookup"><span data-stu-id="b8912-196">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="b8912-197">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="b8912-197">The preceding code:</span></span>

* <span data-ttu-id="b8912-198">, [Uç nokta yönlendirme](#ecors)ile CORS 'yi etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="b8912-198">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="b8912-199">[Varsayılan BIR CORS ilkesi](#dp)tanımlamaz.</span><span class="sxs-lookup"><span data-stu-id="b8912-199">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="b8912-200">Denetleyicinin CORS ilkesini etkinleştirmek için [[Enablecors ("MyPolicy")]](#attr) kullanır `"MyPolicy"` .</span><span class="sxs-lookup"><span data-stu-id="b8912-200">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="b8912-201">Yöntemi için CORS 'yi devre dışı bırakır `GetValues2` .</span><span class="sxs-lookup"><span data-stu-id="b8912-201">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="b8912-202">Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="b8912-202">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="b8912-203">CORS ilke seçenekleri</span><span class="sxs-lookup"><span data-stu-id="b8912-203">CORS policy options</span></span>

<span data-ttu-id="b8912-204">Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="b8912-204">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="b8912-205">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="b8912-205">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="b8912-206">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-206">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="b8912-207">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-207">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="b8912-208">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-208">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="b8912-209">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="b8912-209">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="b8912-210">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-210">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b8912-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b8912-211"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8912-212">Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-212">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="b8912-213">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="b8912-213">Set the allowed origins</span></span>

<span data-ttu-id="b8912-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` .</span><span class="sxs-lookup"><span data-stu-id="b8912-214"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="b8912-215">`AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-215">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b8912-216">`AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-216">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b8912-217">Bir uygulama her iki yöntemle yapılandırıldığında, CORS hizmeti geçersiz bir CORS yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="b8912-217">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="b8912-218">`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="b8912-218">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="b8912-219">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8912-219">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b8912-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b8912-221">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-221">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b8912-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="b8912-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="b8912-223">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="b8912-223">Allows any HTTP method:</span></span>
* <span data-ttu-id="b8912-224">Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="b8912-224">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="b8912-225">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8912-225">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b8912-226">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-226">Set the allowed request headers</span></span>

<span data-ttu-id="b8912-227">Belirli başlıkların, [Yazar isteği üstbilgileri](https://xhr.spec.whatwg.org/#request)ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:</span><span class="sxs-lookup"><span data-stu-id="b8912-227">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="b8912-228">Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-228">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="b8912-229">`AllowAnyHeader`ön kontrol isteklerini ve [erişim-denetim-istek-üst](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) bilgi üst bilgisini etkiler.</span><span class="sxs-lookup"><span data-stu-id="b8912-229">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="b8912-230">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8912-230">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b8912-231">Tarafından belirtilen belirli başlıklarıyla eşleşen bir CORS ara yazılım ilkesi, `WithHeaders` yalnızca `Access-Control-Request-Headers` ' de belirtilen üstbilgiler ile tam olarak eşleşiyorsa mümkündür `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="b8912-231">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="b8912-232">Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="b8912-232">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="b8912-233">CORS ara yazılımı, `Content-Language` ([headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) içinde listelenmediği için aşağıdaki istek üst bilgisi ile bir ön denetim isteğini reddettiğinde `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="b8912-233">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="b8912-234">Uygulama *200 ok* yanıtı DÖNDÜRÜYOR ancak CORS üst bilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="b8912-234">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b8912-235">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="b8912-235">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b8912-236">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-236">Set the exposed response headers</span></span>

<span data-ttu-id="b8912-237">Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz.</span><span class="sxs-lookup"><span data-stu-id="b8912-237">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="b8912-238">Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="b8912-238">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="b8912-239">Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b8912-239">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="b8912-240">CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="b8912-240">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="b8912-241">Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-241">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b8912-242">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="b8912-242">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b8912-243">Kimlik bilgileri CORS isteğinde özel işleme gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b8912-243">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b8912-244">Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez.</span><span class="sxs-lookup"><span data-stu-id="b8912-244">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="b8912-245">Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="b8912-245">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="b8912-246">Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="b8912-246">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="b8912-247">`XMLHttpRequest`Doğrudan kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b8912-247">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b8912-248">JQuery kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b8912-248">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="b8912-249">[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:</span><span class="sxs-lookup"><span data-stu-id="b8912-249">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="b8912-250">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="b8912-250">The server must allow the credentials.</span></span> <span data-ttu-id="b8912-251">Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-251">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="b8912-252">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="b8912-252">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b8912-253">Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-253">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="b8912-254">Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="b8912-254">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="b8912-255">Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-255">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="b8912-256">CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="b8912-256">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="b8912-257">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="b8912-257">Preflight requests</span></span>

<span data-ttu-id="b8912-258">Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir [seçenek](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="b8912-258">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="b8912-259">Bu isteğe bir [ön kontrol isteği](https://developer.mozilla.org/docs/Glossary/Preflight_request)denir.</span><span class="sxs-lookup"><span data-stu-id="b8912-259">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="b8912-260">Aşağıdaki koşulların **Tümü** doğruysa tarayıcı, ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="b8912-260">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="b8912-261">İstek yöntemi al, HEAD veya POST.</span><span class="sxs-lookup"><span data-stu-id="b8912-261">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="b8912-262">Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="b8912-262">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="b8912-263">, `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="b8912-263">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="b8912-264">İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="b8912-264">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="b8912-265">CORS belirtimi, bu üst bilgiler [Yazar istek üst bilgilerini](https://www.w3.org/TR/cors/#author-request-headers)çağırır.</span><span class="sxs-lookup"><span data-stu-id="b8912-265">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="b8912-266">Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="b8912-266">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="b8912-267">Aşağıda, bu belgenin [Test CORS](#testc) bölümündeki **[testi koy]** düğmesinden yapılmış olan ön kontrol isteğine benzer bir yanıt verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b8912-267">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="b8912-268">Ön kontrol isteği [http seçenekleri](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8912-268">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="b8912-269">Aşağıdaki üstbilgiler bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="b8912-269">It may include the following headers:</span></span>

* <span data-ttu-id="b8912-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): gerçek istek IÇIN kullanılacak http yöntemi.</span><span class="sxs-lookup"><span data-stu-id="b8912-270">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="b8912-271">[Erişim-denetim-istek-üstbilgiler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi.</span><span class="sxs-lookup"><span data-stu-id="b8912-271">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="b8912-272">Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="b8912-272">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="b8912-273">Erişim-denetim-Izin-Yöntemler</span><span class="sxs-lookup"><span data-stu-id="b8912-273">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="b8912-274">Ön kontrol isteği reddedilirse, uygulama bir `200 OK` yanıt döndürür ancak CORS üst bilgilerini yapmaz.</span><span class="sxs-lookup"><span data-stu-id="b8912-274">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="b8912-275">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="b8912-275">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="b8912-276">Reddedilen bir ön kontrol isteğine bir örnek için, bu belgenin [Test CORS](#testc) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b8912-276">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="b8912-277">Konsol uygulaması, F12 araçlarını kullanarak tarayıcıya bağlı olarak aşağıdakilerden birine benzer bir hata gösterir:</span><span class="sxs-lookup"><span data-stu-id="b8912-277">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="b8912-278">Firefox: çapraz kaynak Isteği engellendi: aynı kaynak Ilkesi, konumundaki uzak kaynağı okumaktan izin vermez `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="b8912-278">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="b8912-279">(Neden: CORS isteği başarılı olmadı).</span><span class="sxs-lookup"><span data-stu-id="b8912-279">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="b8912-280">Daha Fazla Bilgi</span><span class="sxs-lookup"><span data-stu-id="b8912-280">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="b8912-281">Kmıum tabanlı: ' ' kaynağından ' ' konumundaki getirme erişimi https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS ilkesi tarafından engellendi: ön kontrol Isteğine verilen yanıt erişim denetimi denetimine geçti: İstenen kaynakta ' Access-Control-Allow-Origin ' üst bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="b8912-281">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="b8912-282">Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b8912-282">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="b8912-283">Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-283">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="b8912-284">Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-284">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="b8912-285">Tarayıcılar, nasıl ayarlandıklarından tutarlı değildir `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="b8912-285">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="b8912-286">Aşağıdakilerden biri:</span><span class="sxs-lookup"><span data-stu-id="b8912-286">If either:</span></span>

* <span data-ttu-id="b8912-287">Üst bilgiler, dışında bir şeye ayarlanır`"*"`</span><span class="sxs-lookup"><span data-stu-id="b8912-287">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="b8912-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>çağrıldı: en az `Accept` , `Content-Type` , ve, ve `Origin` desteklemek istediğiniz tüm özel üstbilgileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="b8912-288"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="b8912-289">Otomatik ön kontrol istek kodu</span><span class="sxs-lookup"><span data-stu-id="b8912-289">Automatic preflight request code</span></span>

<span data-ttu-id="b8912-290">CORS ilkesi uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="b8912-290">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="b8912-291">' İ çağırarak küresel olarak `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b8912-291">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="b8912-292">Özniteliği kullanılıyor `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="b8912-292">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="b8912-293">ASP.NET Core, ön kontrol SEÇENEKLERI isteğine yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="b8912-293">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="b8912-294">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` Otomatik ön kontrol **not** istekleri desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="b8912-294">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="b8912-295">Bu belgenin [Test CORS](#testc) bölümü bu davranışı gösterir.</span><span class="sxs-lookup"><span data-stu-id="b8912-295">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="b8912-296">[HttpOptions] ön kontrol istekleri için öznitelik</span><span class="sxs-lookup"><span data-stu-id="b8912-296">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="b8912-297">CORS uygun ilkeyle etkinleştirildiğinde, ASP.NET Core genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-297">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="b8912-298">Bazı senaryolarda bu durum olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-298">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="b8912-299">Örneğin, [uç nokta yönlendirme Ile CORS](#ecors)'yi kullanma.</span><span class="sxs-lookup"><span data-stu-id="b8912-299">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="b8912-300">Aşağıdaki kod, Seçenekler istekleri için uç noktalar oluşturmak için [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="b8912-300">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="b8912-301">Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="b8912-301">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b8912-302">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-302">Set the preflight expiration time</span></span>

<span data-ttu-id="b8912-303">`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="b8912-303">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b8912-304">Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-304">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b8912-305">CORS nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="b8912-305">How CORS works</span></span>

<span data-ttu-id="b8912-306">Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b8912-306">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="b8912-307">CORS bir güvenlik özelliği **değil** .</span><span class="sxs-lookup"><span data-stu-id="b8912-307">CORS is **not** a security feature.</span></span> <span data-ttu-id="b8912-308">CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-308">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="b8912-309">Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyası (XSS)](xref:security/cross-site-scripting) kullanabilir ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-309">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="b8912-310">Bir API, CORS 'ye izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-310">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="b8912-311">CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b8912-311">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="b8912-312">Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir.</span><span class="sxs-lookup"><span data-stu-id="b8912-312">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="b8912-313">Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b8912-313">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="b8912-314">Fiddler</span><span class="sxs-lookup"><span data-stu-id="b8912-314">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="b8912-315">Postman</span><span class="sxs-lookup"><span data-stu-id="b8912-315">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="b8912-316">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="b8912-316">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="b8912-317">Adres çubuğuna URL girerek bir Web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="b8912-317">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="b8912-318">Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="b8912-318">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="b8912-319">CORS içermeyen tarayıcılar, çıkış noktaları arası istekleri yapamıyor.</span><span class="sxs-lookup"><span data-stu-id="b8912-319">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="b8912-320">CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b8912-320">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="b8912-321">JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8912-321">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="b8912-322">Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-322">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="b8912-323">[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="b8912-323">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b8912-324">Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-324">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="b8912-325">CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-325">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="b8912-326">Dağıtılan [örnekteki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [testi Yerleştir düğmesi](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="b8912-326">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="b8912-327">Aşağıda, [Values](https://cors3.azurewebsites.net/) test düğmesinden olan bir çapraz kaynak isteğine bir örnek verilmiştir `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="b8912-327">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="b8912-328">`Origin`Üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="b8912-328">The `Origin` header:</span></span>

* <span data-ttu-id="b8912-329">, İsteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-329">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="b8912-330">Gereklidir ve konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-330">Is required and must be different from the host.</span></span>

<span data-ttu-id="b8912-331">**Genel üstbilgiler**</span><span class="sxs-lookup"><span data-stu-id="b8912-331">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="b8912-332">**Yanıt üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="b8912-332">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="b8912-333">**İstek üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="b8912-333">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

<span data-ttu-id="b8912-334">`OPTIONS`İsteklerde, sunucu yanıtta **yanıt üstbilgileri** `Access-Control-Allow-Origin: {allowed origin}` üst bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-334">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="b8912-335">Örneğin, dağıtılan [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) düğme `OPTIONS` isteği aşağıdaki üstbilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="b8912-335">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="b8912-336">**Genel üstbilgiler**</span><span class="sxs-lookup"><span data-stu-id="b8912-336">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="b8912-337">**Yanıt üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="b8912-337">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="b8912-338">**İstek üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="b8912-338">**Request headers**</span></span>

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

<span data-ttu-id="b8912-339">Önceki **yanıt üst bilgilerinde**sunucu, yanıtta [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) üst bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-339">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="b8912-340">`https://cors1.azurewebsites.net`Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="b8912-340">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="b8912-341"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>Çağrılırsa, `Access-Control-Allow-Origin: *` joker karakter değeri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b8912-341">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="b8912-342">`AllowAnyOrigin`Tüm kaynağa izin verir.</span><span class="sxs-lookup"><span data-stu-id="b8912-342">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="b8912-343">Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-343">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="b8912-344">Özellikle, tarayıcı isteğe izin vermez.</span><span class="sxs-lookup"><span data-stu-id="b8912-344">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b8912-345">Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="b8912-345">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="b8912-346">Görüntüleme SEÇENEKLERI istekleri</span><span class="sxs-lookup"><span data-stu-id="b8912-346">Display OPTIONS requests</span></span>

<span data-ttu-id="b8912-347">Varsayılan olarak, Chrome ve Edge tarayıcıları, F12 araçlarının Ağ sekmesinde seçenek isteklerini göstermez.</span><span class="sxs-lookup"><span data-stu-id="b8912-347">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="b8912-348">Bu tarayıcılarda seçenek isteklerini göstermek için:</span><span class="sxs-lookup"><span data-stu-id="b8912-348">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="b8912-349">`chrome://flags/#out-of-blink-cors` veya `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="b8912-349">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="b8912-350">bayrağı devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="b8912-350">disable the flag.</span></span>
* <span data-ttu-id="b8912-351">uygulamasını.</span><span class="sxs-lookup"><span data-stu-id="b8912-351">restart.</span></span>

<span data-ttu-id="b8912-352">Firefox, varsayılan olarak seçenek isteklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="b8912-352">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="b8912-353">IIS 'de CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-353">CORS in IIS</span></span>

<span data-ttu-id="b8912-354">IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-354">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="b8912-355">Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b8912-355">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="b8912-356">Test CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-356">Test CORS</span></span>

<span data-ttu-id="b8912-357">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) , CORS 'yi test etmek için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="b8912-357">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="b8912-358">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="b8912-358">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="b8912-359">Örnek, eklenen sayfaların bulunduğu bir API projem örneğidir Razor :</span><span class="sxs-lookup"><span data-stu-id="b8912-359">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="b8912-360">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-360">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="b8912-361">Aşağıdakiler, `ValuesController` test için uç noktaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="b8912-361">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="b8912-362">[Mydisplayrouteınfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) , [Rick.Docs. Samples. routeınfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet paketi tarafından sağlanır ve rota bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b8912-362">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="b8912-363">Aşağıdaki yaklaşımlardan birini kullanarak önceki örnek kodu test edin:</span><span class="sxs-lookup"><span data-stu-id="b8912-363">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="b8912-364">Üzerinde dağıtılan örnek uygulamayı kullanın [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="b8912-364">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="b8912-365">Örneği indirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="b8912-365">There is no need to download the sample.</span></span>
* <span data-ttu-id="b8912-366">`dotnet run`Varsayılan URL 'sini kullanarak örneği çalıştırın `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="b8912-366">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="b8912-367">Bir URL 'SI için, bağlantı noktası 44398 olarak ayarlanan Visual Studio 'daki örneği çalıştırın `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="b8912-367">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="b8912-368">F12 araçlarıyla bir tarayıcı kullanma:</span><span class="sxs-lookup"><span data-stu-id="b8912-368">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="b8912-369">**Değerler** düğmesini seçin ve **ağ** sekmesindeki üst bilgileri gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="b8912-369">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="b8912-370">**Testi yerleştir** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="b8912-370">Select the **PUT test** button.</span></span> <span data-ttu-id="b8912-371">Seçenekler isteği görüntüleme yönergeleri için bkz. [görüntüleme seçenekleri istekleri](#options) .</span><span class="sxs-lookup"><span data-stu-id="b8912-371">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="b8912-372">**PUT testi** iki istek, bir seçenekler ön hazırlığı ISTEğI ve PUT isteği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b8912-372">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="b8912-373">**`GetValues2 [DisableCors]`** Başarısız BIR CORS isteğini tetiklemek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="b8912-373">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="b8912-374">Belgede bahsedildiği gibi, yanıt 200 başarılı döndürür, ancak CORS isteği yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="b8912-374">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="b8912-375">CORS hatasını görmek için **konsol** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="b8912-375">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="b8912-376">Tarayıcıya bağlı olarak aşağıdakine benzer bir hata görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b8912-376">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="b8912-377">`'https://cors1.azurewebsites.net/api/values/GetValues2'`Kaynak kimden kaynağı `'https://cors3.azurewebsites.net'` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="b8912-377">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="b8912-378">Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b8912-378">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="b8912-379">CORS özellikli uç noktalar, [kıvrımlı](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-379">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="b8912-380">Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-380">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="b8912-381">İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :</span><span class="sxs-lookup"><span data-stu-id="b8912-381">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="b8912-382">CORS ara yazılımı için isteği işleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="b8912-382">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="b8912-383">CORS üstbilgileri yanıtta döndürülmedi.</span><span class="sxs-lookup"><span data-stu-id="b8912-383">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="b8912-384">Aşağıdaki komut, `curl` bilgileri içeren BIR seçenek isteği vermek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="b8912-384">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="b8912-385">ENDPOINT yönlendirme ve [HttpOptions] ile test CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-385">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="b8912-386">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** .</span><span class="sxs-lookup"><span data-stu-id="b8912-386">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="b8912-387">[CORS 'yi etkinleştirmek için Endpoint Routing](#ecors)kullanan aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="b8912-387">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="b8912-388">Aşağıdakiler `TodoItems1Controller` test için uç noktalar sağlar:</span><span class="sxs-lookup"><span data-stu-id="b8912-388">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="b8912-389">Önceki kodu dağıtılan [Örneğin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Test sayfasından](https://cors1.azurewebsites.net/test?number=1) test edin.</span><span class="sxs-lookup"><span data-stu-id="b8912-389">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="b8912-390">Bitiş noktaları, ön kontrol isteklerini içerdiğinden ve yanıt verdiği için **delete [enablecors]** ve **Get [enablecors]** düğmeleri başarılı olur `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="b8912-390">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="b8912-391">Diğer uç noktalar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-391">The other endpoints fails.</span></span> <span data-ttu-id="b8912-392">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) şu şekilde gönderdiği için **Al** düğmesi başarısız olur:</span><span class="sxs-lookup"><span data-stu-id="b8912-392">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="b8912-393">Aşağıdakiler `TodoItems2Controller` benzer uç noktalar sağlar, ancak seçenek isteklerine yanıt vermek için açık kod içerir:</span><span class="sxs-lookup"><span data-stu-id="b8912-393">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="b8912-394">Önceki kodu dağıtılan örneğin [Test sayfasından](https://cors1.azurewebsites.net/test?number=2) test edin.</span><span class="sxs-lookup"><span data-stu-id="b8912-394">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="b8912-395">**Denetleyici** açılan listesinde, **ön kontrol** ' yı ve ardından **denetleyiciyi ayarla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="b8912-395">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="b8912-396">Uç noktalara yönelik tüm CORS çağrıları `TodoItems2Controller` başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-396">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8912-397">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b8912-397">Additional resources</span></span>

* [<span data-ttu-id="b8912-398">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="b8912-398">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="b8912-399">IIS CORS modülünü kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="b8912-399">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b8912-400">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b8912-400">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b8912-401">Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b8912-401">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="b8912-402">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="b8912-402">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="b8912-403">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-403">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="b8912-404">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="b8912-404">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="b8912-405">Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b8912-405">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="b8912-406">Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="b8912-406">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="b8912-407">[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="b8912-407">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="b8912-408">, Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-408">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="b8912-409">Güvenlik özelliği **değil** , CORS güvenliği.</span><span class="sxs-lookup"><span data-stu-id="b8912-409">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="b8912-410">CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-410">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="b8912-411">Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="b8912-411">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="b8912-412">Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="b8912-412">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="b8912-413">, [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="b8912-413">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="b8912-414">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b8912-414">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="b8912-415">Aynı kaynak</span><span class="sxs-lookup"><span data-stu-id="b8912-415">Same origin</span></span>

<span data-ttu-id="b8912-416">Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="b8912-416">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="b8912-417">Bu iki URL aynı kaynağa sahiptir:</span><span class="sxs-lookup"><span data-stu-id="b8912-417">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="b8912-418">Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:</span><span class="sxs-lookup"><span data-stu-id="b8912-418">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="b8912-419">`https://example.net`: Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="b8912-419">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="b8912-420">`https://www.example.com/foo.html`: Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="b8912-420">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="b8912-421">`http://example.com/foo.html`: Farklı düzen</span><span class="sxs-lookup"><span data-stu-id="b8912-421">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="b8912-422">`https://example.com:9000/foo.html`: Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="b8912-422">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="b8912-423">Internet Explorer, kaynakları karşılaştırırken bağlantı noktasını dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="b8912-423">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="b8912-424">Adlandırılmış ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-424">CORS with named policy and middleware</span></span>

<span data-ttu-id="b8912-425">CORS ara yazılımı, çıkış noktaları arası istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="b8912-425">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="b8912-426">Aşağıdaki kod, belirtilen kaynağa sahip tüm uygulama için CORS 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="b8912-426">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="b8912-427">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="b8912-427">The preceding code:</span></span>

* <span data-ttu-id="b8912-428">İlke adını " \_ myallowspecifickaynaklarına" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-428">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="b8912-429">İlke adı rastgele olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-429">The policy name is arbitrary.</span></span>
* <span data-ttu-id="b8912-430"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS 'yi sağlayan genişletme yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="b8912-430">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="b8912-431"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar.</span><span class="sxs-lookup"><span data-stu-id="b8912-431">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="b8912-432">Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> .</span><span class="sxs-lookup"><span data-stu-id="b8912-432">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="b8912-433">Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="b8912-433">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="b8912-434"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:</span><span class="sxs-lookup"><span data-stu-id="b8912-434">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="b8912-435">Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b8912-435">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="b8912-436"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemi aşağıdaki kodda gösterildiği gibi yöntemleri zincirleyebilir:</span><span class="sxs-lookup"><span data-stu-id="b8912-436">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="b8912-437">Not: URL sonunda eğik **not** çizgi ( `/` ) bulunmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-437">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="b8912-438">URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="b8912-438">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="b8912-439">Aşağıdaki kod, CORS ara yazılımı aracılığıyla CORS ilkelerini tüm uygulama uç noktalarına uygular:</span><span class="sxs-lookup"><span data-stu-id="b8912-439">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
<span data-ttu-id="b8912-440">Note: öğesinden `UseCors` önce çağrılmalıdır `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="b8912-440">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="b8912-441">CORS ilkesini sayfa/denetleyici/eylem düzeyinde uygulamak için bkz. [ Razor sayfalarda, denetleyicilerde ve eylem yöntemlerinde CORS 'yi etkinleştirme](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="b8912-441">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="b8912-442">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="b8912-442">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="b8912-443">CORS 'yi özniteliklerle etkinleştir</span><span class="sxs-lookup"><span data-stu-id="b8912-443">Enable CORS with attributes</span></span>

<span data-ttu-id="b8912-444">[ &lbrack; Enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliği, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-444">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="b8912-445">`[EnableCors]`Öznitelik, tüm bitiş noktaları yerine, seçilen bitiş noktaları IÇIN CORS 'yi mümkün.</span><span class="sxs-lookup"><span data-stu-id="b8912-445">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="b8912-446">`[EnableCors]`Varsayılan ilkeyi belirtmek ve `[EnableCors("{Policy String}")]` bir ilke belirlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8912-446">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="b8912-447">`[EnableCors]`Özniteliği öğesine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="b8912-447">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="b8912-448">Sayfasında`PageModel`</span><span class="sxs-lookup"><span data-stu-id="b8912-448"> Page `PageModel`</span></span>
* <span data-ttu-id="b8912-449">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="b8912-449">Controller</span></span>
* <span data-ttu-id="b8912-450">Denetleyici eylemi yöntemi</span><span class="sxs-lookup"><span data-stu-id="b8912-450">Controller action method</span></span>

<span data-ttu-id="b8912-451">Özniteliği ile denetleyici/sayfa-model/eylem 'e farklı ilkeler uygulayabilirsiniz `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="b8912-451">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="b8912-452">`[EnableCors]`Özniteliği bir denetleyiciler/sayfa modeli/eylem yöntemine uygulandığında ve bu işlem, ara yazılım 'NDA CORS 'yi etkinleştirmişse, **her iki ilke de** uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b8912-452">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="b8912-453">İlkeleri **birleştirmediğimiz** için önerilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-453">We recommend **not** combining policies.</span></span> <span data-ttu-id="b8912-454">`[EnableCors]`Özniteliği veya ara yazılımı kullanın, **her ikisini birden**kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="b8912-454">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="b8912-455">Kullanırken `[EnableCors]` , varsayılan bir **not** ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="b8912-455">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="b8912-456">Aşağıdaki kod her bir yönteme farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="b8912-456">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="b8912-457">Aşağıdaki kod, bir CORS varsayılan ilkesi ve adlı bir ilke oluşturur `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="b8912-457">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="b8912-458">CORS 'yi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b8912-458">Disable CORS</span></span>

<span data-ttu-id="b8912-459">[ &lbrack; Disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği denetleyici/sayfa MODELI/eylem için CORS 'yi devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="b8912-459">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="b8912-460">CORS ilke seçenekleri</span><span class="sxs-lookup"><span data-stu-id="b8912-460">CORS policy options</span></span>

<span data-ttu-id="b8912-461">Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="b8912-461">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="b8912-462">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="b8912-462">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="b8912-463">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-463">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="b8912-464">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-464">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="b8912-465">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-465">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="b8912-466">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="b8912-466">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="b8912-467">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-467">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="b8912-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b8912-468"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b8912-469">Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-469">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="b8912-470">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="b8912-470">Set the allowed origins</span></span>

<span data-ttu-id="b8912-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` .</span><span class="sxs-lookup"><span data-stu-id="b8912-471"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="b8912-472">`AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-472">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="b8912-473">`AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-473">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="b8912-474">Güvenli bir uygulama için, istemcinin sunucu kaynaklarına erişim yetkisi olması gerekiyorsa, kaynakların tam bir listesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="b8912-474">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="b8912-475">`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="b8912-475">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="b8912-476">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8912-476">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b8912-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="b8912-478">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-478">Set the allowed HTTP methods</span></span>

<span data-ttu-id="b8912-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="b8912-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="b8912-480">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="b8912-480">Allows any HTTP method:</span></span>
* <span data-ttu-id="b8912-481">Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="b8912-481">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="b8912-482">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8912-482">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="b8912-483">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-483">Set the allowed request headers</span></span>

<span data-ttu-id="b8912-484">Belirli başlıkların, *Yazar isteği üstbilgileri*ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:</span><span class="sxs-lookup"><span data-stu-id="b8912-484">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b8912-485">Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-485">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b8912-486">Bu ayar, ön kontrol isteklerini ve `Access-Control-Request-Headers` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="b8912-486">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="b8912-487">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="b8912-487">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="b8912-488">CORS ara yazılımı her zaman `Access-Control-Request-Headers` CorsPolicy. Headers ' de yapılandırılan değerlerden bağımsız olarak, ' deki dört üst bilgilerin gönderilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="b8912-488">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="b8912-489">Bu üst bilgi listesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="b8912-489">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="b8912-490">Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="b8912-490">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="b8912-491">CORS ara yazılımı, `Content-Language` her zaman izin verildiği için aşağıdaki istek üstbilgisiyle bir ön kontrol isteğine başarıyla yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="b8912-491">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always permitted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="b8912-492">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-492">Set the exposed response headers</span></span>

<span data-ttu-id="b8912-493">Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz.</span><span class="sxs-lookup"><span data-stu-id="b8912-493">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="b8912-494">Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="b8912-494">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="b8912-495">Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="b8912-495">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="b8912-496">CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="b8912-496">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="b8912-497">Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-497">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="b8912-498">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="b8912-498">Credentials in cross-origin requests</span></span>

<span data-ttu-id="b8912-499">Kimlik bilgileri CORS isteğinde özel işleme gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b8912-499">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="b8912-500">Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez.</span><span class="sxs-lookup"><span data-stu-id="b8912-500">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="b8912-501">Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="b8912-501">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="b8912-502">Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="b8912-502">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="b8912-503">`XMLHttpRequest`Doğrudan kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b8912-503">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="b8912-504">JQuery kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b8912-504">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="b8912-505">[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:</span><span class="sxs-lookup"><span data-stu-id="b8912-505">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="b8912-506">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="b8912-506">The server must allow the credentials.</span></span> <span data-ttu-id="b8912-507">Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-507">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="b8912-508">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="b8912-508">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="b8912-509">Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-509">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="b8912-510">Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="b8912-510">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="b8912-511">Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-511">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="b8912-512">CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="b8912-512">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="b8912-513">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="b8912-513">Preflight requests</span></span>

<span data-ttu-id="b8912-514">Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir istek gönderir.</span><span class="sxs-lookup"><span data-stu-id="b8912-514">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="b8912-515">Bu isteğe bir *ön kontrol isteği*denir.</span><span class="sxs-lookup"><span data-stu-id="b8912-515">This request is called a *preflight request*.</span></span> <span data-ttu-id="b8912-516">Aşağıdaki koşullar doğruysa tarayıcı, ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="b8912-516">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="b8912-517">İstek yöntemi al, HEAD veya POST.</span><span class="sxs-lookup"><span data-stu-id="b8912-517">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="b8912-518">Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="b8912-518">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="b8912-519">, `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="b8912-519">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="b8912-520">İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="b8912-520">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="b8912-521">CORS belirtimi, bu üst bilgiler *Yazar istek üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="b8912-521">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="b8912-522">Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="b8912-522">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="b8912-523">Aşağıda bir ön denetim isteğine örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b8912-523">The following is an example of a preflight request:</span></span>

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

<span data-ttu-id="b8912-524">Uçuş öncesi isteği HTTP SEÇENEKLERI yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8912-524">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="b8912-525">İki özel üst bilgi içerir:</span><span class="sxs-lookup"><span data-stu-id="b8912-525">It includes two special headers:</span></span>

* <span data-ttu-id="b8912-526">`Access-Control-Request-Method`: Gerçek istek için kullanılacak HTTP yöntemi.</span><span class="sxs-lookup"><span data-stu-id="b8912-526">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="b8912-527">`Access-Control-Request-Headers`: Uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi.</span><span class="sxs-lookup"><span data-stu-id="b8912-527">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="b8912-528">Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="b8912-528">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="b8912-529">CORS uygun ilkeyle etkinleştirildiğinde ASP.NET Core, genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-529">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="b8912-530">[Ön kontrol istekleri için bkz. [HttpOptions] özniteliği](#pro).</span><span class="sxs-lookup"><span data-stu-id="b8912-530">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="b8912-531">CORS ön kontrol isteği `Access-Control-Request-Headers` , sunucuya gerçek istekle gönderilen üstbilgileri belirten bir üst bilgi içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-531">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="b8912-532">Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-532">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="b8912-533">Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-533">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="b8912-534">Tarayıcılar, nasıl ayarlandıklarından tamamen tutarlı değildir `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="b8912-534">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="b8912-535">Üst bilgileri `"*"` (veya kullanımı) dışında bir şeye ayarlarsanız, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> desteklemek istediğiniz en az `Accept` , `Content-Type` , ve ve `Origin` Ek üstbilgileri dahil etmelisiniz.</span><span class="sxs-lookup"><span data-stu-id="b8912-535">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="b8912-536">Aşağıda, ön kontrol isteğine örnek bir yanıt verilmiştir (sunucunun isteğe izin verdiği varsayıldığında):</span><span class="sxs-lookup"><span data-stu-id="b8912-536">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

<span data-ttu-id="b8912-537">Yanıt, izin verilen `Access-Control-Allow-Methods` üst bilgileri listeleyen, izin verilen yöntemleri ve isteğe bağlı olarak bir üst bilgiyi listeleyen bir üst bilgi içerir `Access-Control-Allow-Headers` .</span><span class="sxs-lookup"><span data-stu-id="b8912-537">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="b8912-538">Ön kontrol isteği başarılı olursa, tarayıcı gerçek isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="b8912-538">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="b8912-539">Ön kontrol isteği reddedilirse, uygulama *200 ok* yanıtı döndürür ancak CORS üst bilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="b8912-539">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="b8912-540">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="b8912-540">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="b8912-541">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="b8912-541">Set the preflight expiration time</span></span>

<span data-ttu-id="b8912-542">`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="b8912-542">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="b8912-543">Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="b8912-543">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="b8912-544">CORS nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="b8912-544">How CORS works</span></span>

<span data-ttu-id="b8912-545">Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b8912-545">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="b8912-546">CORS bir güvenlik özelliği **değil** .</span><span class="sxs-lookup"><span data-stu-id="b8912-546">CORS is **not** a security feature.</span></span> <span data-ttu-id="b8912-547">CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-547">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="b8912-548">Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyalarını (XSS) engelliyor](xref:security/cross-site-scripting) ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-548">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="b8912-549">CORS 'nin izin vermesini sağlayan API 'niz daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-549">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="b8912-550">CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b8912-550">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="b8912-551">Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir.</span><span class="sxs-lookup"><span data-stu-id="b8912-551">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="b8912-552">Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="b8912-552">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="b8912-553">Fiddler</span><span class="sxs-lookup"><span data-stu-id="b8912-553">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="b8912-554">Postman</span><span class="sxs-lookup"><span data-stu-id="b8912-554">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="b8912-555">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="b8912-555">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="b8912-556">Adres çubuğuna URL girerek bir Web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="b8912-556">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="b8912-557">Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="b8912-557">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="b8912-558">Tarayıcılar (CORS olmadan), çapraz kaynak istekleri yapamıyor.</span><span class="sxs-lookup"><span data-stu-id="b8912-558">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="b8912-559">CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="b8912-559">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="b8912-560">JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8912-560">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="b8912-561">Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-561">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="b8912-562">[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="b8912-562">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="b8912-563">Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-563">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="b8912-564">CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b8912-564">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="b8912-565">Aşağıda, bir çapraz kaynak isteğine bir örnek verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b8912-565">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="b8912-566">`Origin`Üst bilgi, isteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-566">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="b8912-567">`Origin`Üst bilgi gereklidir ve konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-567">The `Origin` header is required and must be different from the host.</span></span>

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

<span data-ttu-id="b8912-568">Sunucu isteğe izin veriyorsa, `Access-Control-Allow-Origin` yanıttaki üstbilgiyi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="b8912-568">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="b8912-569">Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir veya joker değerdir `"*"` , yani herhangi bir kaynağa izin verilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-569">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

<span data-ttu-id="b8912-570">Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="b8912-570">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="b8912-571">Özellikle, tarayıcı isteğe izin vermez.</span><span class="sxs-lookup"><span data-stu-id="b8912-571">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="b8912-572">Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="b8912-572">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="b8912-573">Test CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-573">Test CORS</span></span>

<span data-ttu-id="b8912-574">CORS 'yi sınamak için:</span><span class="sxs-lookup"><span data-stu-id="b8912-574">To test CORS:</span></span>

1. <span data-ttu-id="b8912-575">[BIR API projesi oluşturun](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="b8912-575">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="b8912-576">Alternatif olarak, [örneği de indirebilirsiniz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="b8912-576">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="b8912-577">Bu belgedeki yaklaşımlardan birini kullanarak CORS 'yi etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="b8912-577">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="b8912-578">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b8912-578">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="b8912-579">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-579">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="b8912-580">Web uygulaması projesi ( Razor Sayfalar veya Mvc) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b8912-580">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="b8912-581">Örnek, Razor sayfaları kullanır.</span><span class="sxs-lookup"><span data-stu-id="b8912-581">The sample uses Razor Pages.</span></span> <span data-ttu-id="b8912-582">Web uygulamasını, API projesiyle aynı çözümde oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="b8912-582">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="b8912-583">Aşağıdaki Vurgulanan kodu *Index. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="b8912-583">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-cshtml[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="b8912-584">Yukarıdaki kodda, `url: 'https://<web app>.azurewebsites.net/api/values/1',` dağıtılan UYGULAMANıN URL 'siyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="b8912-584">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="b8912-585">API projesini dağıtın.</span><span class="sxs-lookup"><span data-stu-id="b8912-585">Deploy the API project.</span></span> <span data-ttu-id="b8912-586">Örneğin, [Azure 'a dağıtın](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="b8912-586">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="b8912-587">RazorMasaüstünden sayfaları veya MVC uygulamasını çalıştırın ve **Test** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="b8912-587">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="b8912-588">Hata iletilerini gözden geçirmek için F12 araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8912-588">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="b8912-589">Localhost kaynağını kaldırın `WithOrigins` ve uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="b8912-589">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="b8912-590">Alternatif olarak, istemci uygulamasını farklı bir bağlantı noktasıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b8912-590">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="b8912-591">Örneğin, Visual Studio 'dan çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="b8912-591">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="b8912-592">İstemci uygulamasıyla test edin.</span><span class="sxs-lookup"><span data-stu-id="b8912-592">Test with the client app.</span></span> <span data-ttu-id="b8912-593">CORS hataları bir hata döndürüyor, ancak hata iletisi JavaScript için kullanılabilir değil.</span><span class="sxs-lookup"><span data-stu-id="b8912-593">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="b8912-594">Hatayı görmek için F12 araçlarındaki konsol sekmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b8912-594">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="b8912-595">Tarayıcıya bağlı olarak, aşağıdakine benzer bir hata alırsınız (F12 araçları konsolunda):</span><span class="sxs-lookup"><span data-stu-id="b8912-595">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="b8912-596">Microsoft Edge 'i kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b8912-596">Using Microsoft Edge:</span></span>

     <span data-ttu-id="b8912-597">**SEC7120: [CORS] kaynak, `https://localhost:44375` `https://localhost:44375` ' deki çıkış noktaları için erişim-denetim-Izin-kaynak yanıt üstbilgisinde bulunamadı`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="b8912-597">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="b8912-598">Chrome kullanarak:</span><span class="sxs-lookup"><span data-stu-id="b8912-598">Using Chrome:</span></span>

     <span data-ttu-id="b8912-599">**Kaynak başında XMLHttpRequest erişimi `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok.**</span><span class="sxs-lookup"><span data-stu-id="b8912-599">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="b8912-600">CORS özellikli uç noktalar [Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="b8912-600">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="b8912-601">Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-601">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="b8912-602">İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :</span><span class="sxs-lookup"><span data-stu-id="b8912-602">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="b8912-603">CORS ara yazılımı için isteği işleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="b8912-603">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="b8912-604">CORS üstbilgileri yanıtta döndürülmedi.</span><span class="sxs-lookup"><span data-stu-id="b8912-604">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="b8912-605">IIS 'de CORS</span><span class="sxs-lookup"><span data-stu-id="b8912-605">CORS in IIS</span></span>

<span data-ttu-id="b8912-606">IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b8912-606">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="b8912-607">Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="b8912-607">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b8912-608">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b8912-608">Additional resources</span></span>

* [<span data-ttu-id="b8912-609">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="b8912-609">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="b8912-610">IIS CORS modülünü kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="b8912-610">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
