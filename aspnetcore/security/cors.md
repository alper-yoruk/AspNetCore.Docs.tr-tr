---
title: ASP.NET Core 'de çıkış noktaları arası Istekleri (CORS) etkinleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında çapraz kaynak isteklerini izin vermek veya reddetmek için CORS 'nin nasıl standart olduğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/cors
ms.openlocfilehash: a78aff2d2e16f36ed034e6af110d7ed763271583
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84105759"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="d4376-103">ASP.NET Core 'de çıkış noktaları arası Istekleri (CORS) etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="d4376-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d4376-104">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="d4376-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="d4376-105">Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d4376-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="d4376-106">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="d4376-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="d4376-107">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="d4376-108">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="d4376-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="d4376-109">Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4376-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="d4376-110">Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="d4376-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="d4376-111">[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="d4376-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="d4376-112">, Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="d4376-113">Güvenlik özelliği **değil** , CORS güvenliği.</span><span class="sxs-lookup"><span data-stu-id="d4376-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="d4376-114">CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="d4376-115">Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="d4376-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="d4376-116">Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d4376-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="d4376-117">, [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="d4376-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="d4376-118">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4376-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="d4376-119">Aynı kaynak</span><span class="sxs-lookup"><span data-stu-id="d4376-119">Same origin</span></span>

<span data-ttu-id="d4376-120">Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="d4376-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="d4376-121">Bu iki URL aynı kaynağa sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d4376-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="d4376-122">Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:</span><span class="sxs-lookup"><span data-stu-id="d4376-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="d4376-123">`https://example.net`: Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="d4376-123">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="d4376-124">`https://www.example.com/foo.html`: Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="d4376-124">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="d4376-125">`http://example.com/foo.html`: Farklı düzen</span><span class="sxs-lookup"><span data-stu-id="d4376-125">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="d4376-126">`https://example.com:9000/foo.html`: Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="d4376-126">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="d4376-127">CORS'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="d4376-127">Enable CORS</span></span>

<span data-ttu-id="d4376-128">CORS 'yi etkinleştirmenin üç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="d4376-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="d4376-129">[Adlandırılmış bir ilke](#np) veya [varsayılan ilke](#dp)kullanan ara yazılımlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="d4376-130">[Uç nokta yönlendirme](#ecors)kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="d4376-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="d4376-131">[[Enablecors]](#attr) özniteliğiyle birlikte.</span><span class="sxs-lookup"><span data-stu-id="d4376-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="d4376-132">Adlandırılmış ilkeyle [[Enablecors]](#attr) ÖZNITELIĞINI kullanmak CORS 'yi destekleyen uç noktaları sınırlayan en yoğun denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="d4376-133">Her yaklaşım aşağıdaki bölümlerde ayrıntılı olarak verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d4376-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="d4376-134">Adlandırılmış ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="d4376-135">CORS ara yazılımı, çıkış noktaları arası istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="d4376-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="d4376-136">Aşağıdaki kod, belirtilen kaynakları kullanarak tüm uygulamanın uç noktalarına bir CORS ilkesi uygular:</span><span class="sxs-lookup"><span data-stu-id="d4376-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="d4376-137">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d4376-137">The preceding code:</span></span>

* <span data-ttu-id="d4376-138">İlke adını olarak ayarlar `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="d4376-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="d4376-139">İlke adı rastgele olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="d4376-140"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>Uzantı yöntemini çağırır ve `_myAllowSpecificOrigins` CORS ilkesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="d4376-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="d4376-141">`UseCors`CORS ara yazılımını ekler.</span><span class="sxs-lookup"><span data-stu-id="d4376-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="d4376-142">Çağrısının `UseCors` sonra `UseRouting` , öncesi yerleştirilmesi gerekir `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="d4376-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="d4376-143">Daha fazla bilgi için bkz. [Ara yazılım sırası](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="d4376-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="d4376-144"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar.</span><span class="sxs-lookup"><span data-stu-id="d4376-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="d4376-145">Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d4376-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="d4376-146">Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d4376-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="d4376-147">`_myAllowSpecificOrigins`Tüm denetleyici uç noktaları için CORS ilkesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="d4376-148">Belirli uç noktalara bir CORS ilkesi uygulamak için bkz. [Endpoint Routing](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="d4376-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="d4376-149">Endpoint Routing ile **CORS ara yazılımı** , ve çağrıları arasında yürütülecek şekilde yapılandırılmalıdır `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="d4376-149">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="d4376-150">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="d4376-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="d4376-151"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:</span><span class="sxs-lookup"><span data-stu-id="d4376-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="d4376-152">Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d4376-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="d4376-153"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemler aşağıdaki kodda gösterildiği gibi zincirleme olabilir:</span><span class="sxs-lookup"><span data-stu-id="d4376-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="d4376-154">Not: belirtilen URL sonunda eğik **not** çizgi ( `/` ) içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="d4376-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="d4376-155">URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="d4376-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="d4376-156">Varsayılan ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="d4376-157">Aşağıdaki vurgulanan kod varsayılan CORS ilkesini sunar:</span><span class="sxs-lookup"><span data-stu-id="d4376-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="d4376-158">Yukarıdaki kod, tüm denetleyici uç noktalarına varsayılan CORS ilkesini uygular.</span><span class="sxs-lookup"><span data-stu-id="d4376-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="d4376-159">Uç nokta yönlendirme ile CORS 'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="d4376-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="d4376-160">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** .</span><span class="sxs-lookup"><span data-stu-id="d4376-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="d4376-161">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/20709) bakın ve [uç nokta yönlendirme ve [HttpOptions] ile test CORS 'yi test](#tcer)edin.</span><span class="sxs-lookup"><span data-stu-id="d4376-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="d4376-162">Uç nokta yönlendirmesinde, CORS uzantı yöntemleri kümesi kullanılarak uç nokta temelinde etkinleştirilebilir <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="d4376-163">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="d4376-163">In the preceding code:</span></span>

* <span data-ttu-id="d4376-164">`app.UseCors`CORS ara yazılımını sunar.</span><span class="sxs-lookup"><span data-stu-id="d4376-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="d4376-165">Varsayılan bir ilke yapılandırılmadığından, `app.UseCors()` tek BAŞıNA CORS 'yi etkinleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="d4376-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="d4376-166">`/echo`Ve denetleyici uç noktaları, belirtilen ilkeyi kullanarak çıkış noktaları arası isteklere izin verir.</span><span class="sxs-lookup"><span data-stu-id="d4376-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="d4376-167">`/echo2` Razor Varsayılan ilke belirtilmediğinden ve sayfa uç noktaları, çıkış noktaları arası isteklere izin **vermez** .</span><span class="sxs-lookup"><span data-stu-id="d4376-167">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="d4376-168">[[Disablecors]](#dc) özniteliği, ile Endpoint Routing tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="d4376-168">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="d4376-169">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="d4376-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="d4376-170">CORS 'yi özniteliklerle etkinleştir</span><span class="sxs-lookup"><span data-stu-id="d4376-170">Enable CORS with attributes</span></span>

<span data-ttu-id="d4376-171">CORS 'yi [[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliğiyle etkinleştirmek ve yalnızca CORS gerektiren uç noktalara adlandırılmış bir ilke uygulamak, en derin denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="d4376-172">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) ÖZNITELIĞI, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="d4376-173">`[EnableCors]`Öznitelik, tüm uç noktalar yerine seçili uç noktalar IÇIN CORS 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="d4376-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="d4376-174">`[EnableCors]`varsayılan ilkeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="d4376-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="d4376-175">`[EnableCors("{Policy String}")]`adlandırılmış bir ilke belirtir.</span><span class="sxs-lookup"><span data-stu-id="d4376-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="d4376-176">`[EnableCors]`Özniteliği öğesine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="d4376-176">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="d4376-177">Sayfasında`PageModel`</span><span class="sxs-lookup"><span data-stu-id="d4376-177"> Page `PageModel`</span></span>
* <span data-ttu-id="d4376-178">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="d4376-178">Controller</span></span>
* <span data-ttu-id="d4376-179">Denetleyici eylemi yöntemi</span><span class="sxs-lookup"><span data-stu-id="d4376-179">Controller action method</span></span>

<span data-ttu-id="d4376-180">Öznitelikleri olan denetleyicilere, sayfa modellerine veya eylem yöntemlerine farklı ilkeler uygulanabilir `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="d4376-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="d4376-181">`[EnableCors]`Öznitelik bir denetleyiciye, sayfa modeline veya eylem yöntemine uygulandığında ve bir ara yazılım IÇINDE CORS etkinleştirildiğinde, **her iki ilke de** uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d4376-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="d4376-182">\*\*İlkelerin birleştirilmesi önerilir. \*\* `[EnableCors]` **Aynı uygulamada değil, özniteliğini veya ara yazılımını kullanın.**</span><span class="sxs-lookup"><span data-stu-id="d4376-182">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="d4376-183">Aşağıdaki kod her bir yönteme farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="d4376-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="d4376-184">Aşağıdaki kod iki CORS ilkesi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="d4376-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="d4376-185">CORS isteklerini sınırlayan en yoğun denetim için:</span><span class="sxs-lookup"><span data-stu-id="d4376-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="d4376-186">`[EnableCors("MyPolicy")]`Adlandırılmış ilkeyle kullanın.</span><span class="sxs-lookup"><span data-stu-id="d4376-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="d4376-187">Varsayılan bir ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="d4376-187">Don't define a default policy.</span></span>
* <span data-ttu-id="d4376-188">[Endpoint Routing](#ecors)kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="d4376-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="d4376-189">Sonraki bölümdeki kod, önceki listeyi karşılar.</span><span class="sxs-lookup"><span data-stu-id="d4376-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="d4376-190">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="d4376-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="d4376-191">CORS 'yi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="d4376-191">Disable CORS</span></span>

<span data-ttu-id="d4376-192">[[Disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği [Endpoint ROUTING](#ecors)tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** .</span><span class="sxs-lookup"><span data-stu-id="d4376-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="d4376-193">Aşağıdaki kod CORS ilkesini tanımlar `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="d4376-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="d4376-194">Aşağıdaki kod, eylem için CORS 'yi devre dışı bırakır `GetValues2` :</span><span class="sxs-lookup"><span data-stu-id="d4376-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="d4376-195">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d4376-195">The preceding code:</span></span>

* <span data-ttu-id="d4376-196">, [Uç nokta yönlendirme](#ecors)ile CORS 'yi etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="d4376-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="d4376-197">[Varsayılan BIR CORS ilkesi](#dp)tanımlamaz.</span><span class="sxs-lookup"><span data-stu-id="d4376-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="d4376-198">Denetleyicinin CORS ilkesini etkinleştirmek için [[Enablecors ("MyPolicy")]](#attr) kullanır `"MyPolicy"` .</span><span class="sxs-lookup"><span data-stu-id="d4376-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="d4376-199">Yöntemi için CORS 'yi devre dışı bırakır `GetValues2` .</span><span class="sxs-lookup"><span data-stu-id="d4376-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="d4376-200">Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="d4376-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="d4376-201">CORS ilke seçenekleri</span><span class="sxs-lookup"><span data-stu-id="d4376-201">CORS policy options</span></span>

<span data-ttu-id="d4376-202">Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="d4376-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="d4376-203">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="d4376-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="d4376-204">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="d4376-205">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="d4376-206">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="d4376-207">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="d4376-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="d4376-208">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="d4376-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d4376-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4376-210">Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="d4376-211">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="d4376-211">Set the allowed origins</span></span>

<span data-ttu-id="d4376-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` .</span><span class="sxs-lookup"><span data-stu-id="d4376-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="d4376-213">`AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="d4376-214">`AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="d4376-215">Bir uygulama her iki yöntemle yapılandırıldığında, CORS hizmeti geçersiz bir CORS yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="d4376-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="d4376-216">`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="d4376-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="d4376-217">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="d4376-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="d4376-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="d4376-219">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="d4376-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="d4376-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="d4376-221">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="d4376-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="d4376-222">Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="d4376-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="d4376-223">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="d4376-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="d4376-224">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-224">Set the allowed request headers</span></span>

<span data-ttu-id="d4376-225">Belirli başlıkların, [Yazar isteği üstbilgileri](https://xhr.spec.whatwg.org/#request)ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:</span><span class="sxs-lookup"><span data-stu-id="d4376-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="d4376-226">Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="d4376-227">`AllowAnyHeader`ön kontrol isteklerini ve [erişim-denetim-istek-üst](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) bilgi üst bilgisini etkiler.</span><span class="sxs-lookup"><span data-stu-id="d4376-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="d4376-228">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="d4376-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="d4376-229">Tarafından belirtilen belirli başlıklarıyla eşleşen bir CORS ara yazılım ilkesi, `WithHeaders` yalnızca `Access-Control-Request-Headers` ' de belirtilen üstbilgiler ile tam olarak eşleşiyorsa mümkündür `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="d4376-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="d4376-230">Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d4376-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="d4376-231">CORS ara yazılımı, `Content-Language` ([headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) içinde listelenmediği için aşağıdaki istek üst bilgisi ile bir ön denetim isteğini reddettiğinde `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="d4376-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="d4376-232">Uygulama *200 ok* yanıtı DÖNDÜRÜYOR ancak CORS üst bilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="d4376-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="d4376-233">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="d4376-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="d4376-234">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-234">Set the exposed response headers</span></span>

<span data-ttu-id="d4376-235">Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz.</span><span class="sxs-lookup"><span data-stu-id="d4376-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="d4376-236">Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="d4376-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="d4376-237">Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d4376-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="d4376-238">CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="d4376-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="d4376-239">Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="d4376-240">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="d4376-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="d4376-241">Kimlik bilgileri CORS isteğinde özel işleme gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d4376-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="d4376-242">Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez.</span><span class="sxs-lookup"><span data-stu-id="d4376-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="d4376-243">Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d4376-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="d4376-244">Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="d4376-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="d4376-245">`XMLHttpRequest`Doğrudan kullanarak:</span><span class="sxs-lookup"><span data-stu-id="d4376-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="d4376-246">JQuery kullanarak:</span><span class="sxs-lookup"><span data-stu-id="d4376-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="d4376-247">[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:</span><span class="sxs-lookup"><span data-stu-id="d4376-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="d4376-248">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="d4376-248">The server must allow the credentials.</span></span> <span data-ttu-id="d4376-249">Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="d4376-250">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="d4376-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="d4376-251">Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="d4376-252">Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="d4376-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="d4376-253">Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="d4376-254">CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="d4376-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="d4376-255">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="d4376-255">Preflight requests</span></span>

<span data-ttu-id="d4376-256">Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir [seçenek](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="d4376-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="d4376-257">Bu isteğe bir [ön kontrol isteği](https://developer.mozilla.org/docs/Glossary/Preflight_request)denir.</span><span class="sxs-lookup"><span data-stu-id="d4376-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="d4376-258">Aşağıdaki koşulların **Tümü** doğruysa tarayıcı, ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="d4376-258">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="d4376-259">İstek yöntemi al, HEAD veya POST.</span><span class="sxs-lookup"><span data-stu-id="d4376-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="d4376-260">Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="d4376-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="d4376-261">, `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="d4376-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="d4376-262">İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="d4376-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="d4376-263">CORS belirtimi, bu üst bilgiler [Yazar istek üst bilgilerini](https://www.w3.org/TR/cors/#author-request-headers)çağırır.</span><span class="sxs-lookup"><span data-stu-id="d4376-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="d4376-264">Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="d4376-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="d4376-265">Aşağıda, bu belgenin [Test CORS](#testc) bölümündeki **[testi koy]** düğmesinden yapılmış olan ön kontrol isteğine benzer bir yanıt verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d4376-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="d4376-266">Ön kontrol isteği [http seçenekleri](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4376-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="d4376-267">Aşağıdaki üstbilgiler bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="d4376-267">It may include the following headers:</span></span>

* <span data-ttu-id="d4376-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): gerçek istek IÇIN kullanılacak http yöntemi.</span><span class="sxs-lookup"><span data-stu-id="d4376-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="d4376-269">[Erişim-denetim-istek-üstbilgiler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi.</span><span class="sxs-lookup"><span data-stu-id="d4376-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="d4376-270">Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="d4376-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="d4376-271">Erişim-denetim-Izin-Yöntemler</span><span class="sxs-lookup"><span data-stu-id="d4376-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="d4376-272">Ön kontrol isteği reddedilirse, uygulama bir `200 OK` yanıt döndürür ancak CORS üst bilgilerini yapmaz.</span><span class="sxs-lookup"><span data-stu-id="d4376-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="d4376-273">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="d4376-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="d4376-274">Reddedilen bir ön kontrol isteğine bir örnek için, bu belgenin [Test CORS](#testc) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d4376-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="d4376-275">Konsol uygulaması, F12 araçlarını kullanarak tarayıcıya bağlı olarak aşağıdakilerden birine benzer bir hata gösterir:</span><span class="sxs-lookup"><span data-stu-id="d4376-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="d4376-276">Firefox: çapraz kaynak Isteği engellendi: aynı kaynak Ilkesi, konumundaki uzak kaynağı okumaktan izin vermez `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="d4376-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="d4376-277">(Neden: CORS isteği başarılı olmadı).</span><span class="sxs-lookup"><span data-stu-id="d4376-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="d4376-278">Daha Fazla Bilgi</span><span class="sxs-lookup"><span data-stu-id="d4376-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="d4376-279">Kmıum tabanlı: ' ' kaynağından ' ' konumundaki getirme erişimi https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS ilkesi tarafından engellendi: ön kontrol Isteğine verilen yanıt erişim denetimi denetimine geçti: İstenen kaynakta ' Access-Control-Allow-Origin ' üst bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="d4376-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="d4376-280">Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d4376-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="d4376-281">Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="d4376-282">Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="d4376-283">Tarayıcılar, nasıl ayarlandıklarından tutarlı değildir `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="d4376-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="d4376-284">Aşağıdakilerden biri:</span><span class="sxs-lookup"><span data-stu-id="d4376-284">If either:</span></span>

* <span data-ttu-id="d4376-285">Üst bilgiler, dışında bir şeye ayarlanır`"*"`</span><span class="sxs-lookup"><span data-stu-id="d4376-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="d4376-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>çağrıldı: en az `Accept` , `Content-Type` , ve, ve `Origin` desteklemek istediğiniz tüm özel üstbilgileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="d4376-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="d4376-287">Otomatik ön kontrol istek kodu</span><span class="sxs-lookup"><span data-stu-id="d4376-287">Automatic preflight request code</span></span>

<span data-ttu-id="d4376-288">CORS ilkesi uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="d4376-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="d4376-289">' İ çağırarak küresel olarak `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="d4376-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="d4376-290">Özniteliği kullanılıyor `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="d4376-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="d4376-291">ASP.NET Core, ön kontrol SEÇENEKLERI isteğine yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="d4376-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="d4376-292">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` Otomatik ön kontrol **not** istekleri desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="d4376-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="d4376-293">Bu belgenin [Test CORS](#testc) bölümü bu davranışı gösterir.</span><span class="sxs-lookup"><span data-stu-id="d4376-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="d4376-294">[HttpOptions] ön kontrol istekleri için öznitelik</span><span class="sxs-lookup"><span data-stu-id="d4376-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="d4376-295">CORS uygun ilkeyle etkinleştirildiğinde, ASP.NET Core genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="d4376-296">Bazı senaryolarda bu durum olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="d4376-297">Örneğin, [uç nokta yönlendirme Ile CORS](#ecors)'yi kullanma.</span><span class="sxs-lookup"><span data-stu-id="d4376-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="d4376-298">Aşağıdaki kod, Seçenekler istekleri için uç noktalar oluşturmak için [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="d4376-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="d4376-299">Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="d4376-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="d4376-300">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-300">Set the preflight expiration time</span></span>

<span data-ttu-id="d4376-301">`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="d4376-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="d4376-302">Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="d4376-303">CORS nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="d4376-303">How CORS works</span></span>

<span data-ttu-id="d4376-304">Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d4376-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="d4376-305">CORS bir güvenlik özelliği **değil** .</span><span class="sxs-lookup"><span data-stu-id="d4376-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="d4376-306">CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="d4376-307">Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyası (XSS)](xref:security/cross-site-scripting) kullanabilir ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="d4376-308">Bir API, CORS 'ye izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="d4376-309">CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d4376-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="d4376-310">Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir.</span><span class="sxs-lookup"><span data-stu-id="d4376-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="d4376-311">Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d4376-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="d4376-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="d4376-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="d4376-313">Postman</span><span class="sxs-lookup"><span data-stu-id="d4376-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="d4376-314">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="d4376-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="d4376-315">Adres çubuğuna URL girerek bir Web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="d4376-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="d4376-316">Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="d4376-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="d4376-317">CORS içermeyen tarayıcılar, çıkış noktaları arası istekleri yapamıyor.</span><span class="sxs-lookup"><span data-stu-id="d4376-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="d4376-318">CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d4376-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="d4376-319">JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4376-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="d4376-320">Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="d4376-321">[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="d4376-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="d4376-322">Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="d4376-323">CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="d4376-324">Dağıtılan [örnekteki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [testi Yerleştir düğmesi](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="d4376-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="d4376-325">Aşağıda, [Values](https://cors3.azurewebsites.net/) test düğmesinden olan bir çapraz kaynak isteğine bir örnek verilmiştir `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="d4376-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="d4376-326">`Origin`Üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="d4376-326">The `Origin` header:</span></span>

* <span data-ttu-id="d4376-327">, İsteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="d4376-328">Gereklidir ve konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="d4376-329">**Genel üstbilgiler**</span><span class="sxs-lookup"><span data-stu-id="d4376-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="d4376-330">**Yanıt üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="d4376-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="d4376-331">**İstek üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="d4376-331">**Request headers**</span></span>

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

<span data-ttu-id="d4376-332">`OPTIONS`İsteklerde, sunucu yanıtta **yanıt üstbilgileri** `Access-Control-Allow-Origin: {allowed origin}` üst bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="d4376-333">Örneğin, dağıtılan [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) düğme `OPTIONS` isteği aşağıdaki üstbilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="d4376-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="d4376-334">**Genel üstbilgiler**</span><span class="sxs-lookup"><span data-stu-id="d4376-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="d4376-335">**Yanıt üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="d4376-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="d4376-336">**İstek üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="d4376-336">**Request headers**</span></span>

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

<span data-ttu-id="d4376-337">Önceki **yanıt üst bilgilerinde**sunucu, yanıtta [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) üst bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="d4376-338">`https://cors1.azurewebsites.net`Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="d4376-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="d4376-339"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>Çağrılırsa, `Access-Control-Allow-Origin: *` joker karakter değeri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="d4376-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="d4376-340">`AllowAnyOrigin`Tüm kaynağa izin verir.</span><span class="sxs-lookup"><span data-stu-id="d4376-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="d4376-341">Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="d4376-342">Özellikle, tarayıcı isteğe izin vermez.</span><span class="sxs-lookup"><span data-stu-id="d4376-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="d4376-343">Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="d4376-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="d4376-344">Görüntüleme SEÇENEKLERI istekleri</span><span class="sxs-lookup"><span data-stu-id="d4376-344">Display OPTIONS requests</span></span>

<span data-ttu-id="d4376-345">Varsayılan olarak, Chrome ve Edge tarayıcıları, F12 araçlarının Ağ sekmesinde seçenek isteklerini göstermez.</span><span class="sxs-lookup"><span data-stu-id="d4376-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="d4376-346">Bu tarayıcılarda seçenek isteklerini göstermek için:</span><span class="sxs-lookup"><span data-stu-id="d4376-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="d4376-347">`chrome://flags/#out-of-blink-cors` veya `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="d4376-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="d4376-348">bayrağı devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="d4376-348">disable the flag.</span></span>
* <span data-ttu-id="d4376-349">uygulamasını.</span><span class="sxs-lookup"><span data-stu-id="d4376-349">restart.</span></span>

<span data-ttu-id="d4376-350">Firefox, varsayılan olarak seçenek isteklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="d4376-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="d4376-351">IIS 'de CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-351">CORS in IIS</span></span>

<span data-ttu-id="d4376-352">IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="d4376-353">Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d4376-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="d4376-354">Test CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-354">Test CORS</span></span>

<span data-ttu-id="d4376-355">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) , CORS 'yi test etmek için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="d4376-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="d4376-356">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="d4376-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="d4376-357">Örnek, eklenen sayfaların bulunduğu bir API projem örneğidir Razor :</span><span class="sxs-lookup"><span data-stu-id="d4376-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="d4376-358">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="d4376-359">Aşağıdakiler, `ValuesController` test için uç noktaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="d4376-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="d4376-360">[Mydisplayrouteınfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) , [Rick. docs. Samples. routeınfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet paketi tarafından sağlanır ve rota bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="d4376-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="d4376-361">Aşağıdaki yaklaşımlardan birini kullanarak önceki örnek kodu test edin:</span><span class="sxs-lookup"><span data-stu-id="d4376-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="d4376-362">Üzerinde dağıtılan örnek uygulamayı kullanın [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="d4376-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="d4376-363">Örneği indirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="d4376-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="d4376-364">`dotnet run`Varsayılan URL 'sini kullanarak örneği çalıştırın `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="d4376-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="d4376-365">Bir URL 'SI için, bağlantı noktası 44398 olarak ayarlanan Visual Studio 'daki örneği çalıştırın `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="d4376-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="d4376-366">F12 araçlarıyla bir tarayıcı kullanma:</span><span class="sxs-lookup"><span data-stu-id="d4376-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="d4376-367">**Değerler** düğmesini seçin ve **ağ** sekmesindeki üst bilgileri gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="d4376-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="d4376-368">**Testi yerleştir** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d4376-368">Select the **PUT test** button.</span></span> <span data-ttu-id="d4376-369">Seçenekler isteği görüntüleme yönergeleri için bkz. [görüntüleme seçenekleri istekleri](#options) .</span><span class="sxs-lookup"><span data-stu-id="d4376-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="d4376-370">**PUT testi** iki istek, bir seçenekler ön hazırlığı ISTEğI ve PUT isteği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="d4376-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="d4376-371">**`GetValues2 [DisableCors]`** Başarısız BIR CORS isteğini tetiklemek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="d4376-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="d4376-372">Belgede bahsedildiği gibi, yanıt 200 başarılı döndürür, ancak CORS isteği yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="d4376-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="d4376-373">CORS hatasını görmek için **konsol** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="d4376-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="d4376-374">Tarayıcıya bağlı olarak aşağıdakine benzer bir hata görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d4376-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="d4376-375">`'https://cors1.azurewebsites.net/api/values/GetValues2'`Kaynak kimden kaynağı `'https://cors3.azurewebsites.net'` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="d4376-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="d4376-376">Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="d4376-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="d4376-377">CORS özellikli uç noktalar, [kıvrımlı](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="d4376-378">Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="d4376-379">İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :</span><span class="sxs-lookup"><span data-stu-id="d4376-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="d4376-380">CORS ara yazılımı için isteği işleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="d4376-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="d4376-381">CORS üstbilgileri yanıtta döndürülmedi.</span><span class="sxs-lookup"><span data-stu-id="d4376-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="d4376-382">Aşağıdaki komut, `curl` bilgileri içeren BIR seçenek isteği vermek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="d4376-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="d4376-383">ENDPOINT yönlendirme ve [HttpOptions] ile test CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="d4376-384">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** .</span><span class="sxs-lookup"><span data-stu-id="d4376-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="d4376-385">[CORS 'yi etkinleştirmek için Endpoint Routing](#ecors)kullanan aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d4376-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="d4376-386">Aşağıdakiler `TodoItems1Controller` test için uç noktalar sağlar:</span><span class="sxs-lookup"><span data-stu-id="d4376-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="d4376-387">Önceki kodu dağıtılan [Örneğin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Test sayfasından](https://cors1.azurewebsites.net/test?number=1) test edin.</span><span class="sxs-lookup"><span data-stu-id="d4376-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="d4376-388">Bitiş noktaları, ön kontrol isteklerini içerdiğinden ve yanıt verdiği için **delete [enablecors]** ve **Get [enablecors]** düğmeleri başarılı olur `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="d4376-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="d4376-389">Diğer uç noktalar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-389">The other endpoints fails.</span></span> <span data-ttu-id="d4376-390">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) şu şekilde gönderdiği için **Al** düğmesi başarısız olur:</span><span class="sxs-lookup"><span data-stu-id="d4376-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="d4376-391">Aşağıdakiler `TodoItems2Controller` benzer uç noktalar sağlar, ancak seçenek isteklerine yanıt vermek için açık kod içerir:</span><span class="sxs-lookup"><span data-stu-id="d4376-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="d4376-392">Önceki kodu dağıtılan örneğin [Test sayfasından](https://cors1.azurewebsites.net/test?number=2) test edin.</span><span class="sxs-lookup"><span data-stu-id="d4376-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="d4376-393">**Denetleyici** açılan listesinde, **ön kontrol** ' yı ve ardından **denetleyiciyi ayarla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="d4376-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="d4376-394">Uç noktalara yönelik tüm CORS çağrıları `TodoItems2Controller` başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4376-395">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d4376-395">Additional resources</span></span>

* [<span data-ttu-id="d4376-396">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="d4376-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="d4376-397">IIS CORS modülünü kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="d4376-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d4376-398">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d4376-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d4376-399">Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="d4376-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="d4376-400">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="d4376-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="d4376-401">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="d4376-402">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="d4376-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="d4376-403">Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4376-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="d4376-404">Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="d4376-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="d4376-405">[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="d4376-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="d4376-406">, Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="d4376-407">Güvenlik özelliği **değil** , CORS güvenliği.</span><span class="sxs-lookup"><span data-stu-id="d4376-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="d4376-408">CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="d4376-409">Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="d4376-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="d4376-410">Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="d4376-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="d4376-411">, [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="d4376-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="d4376-412">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="d4376-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="d4376-413">Aynı kaynak</span><span class="sxs-lookup"><span data-stu-id="d4376-413">Same origin</span></span>

<span data-ttu-id="d4376-414">Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="d4376-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="d4376-415">Bu iki URL aynı kaynağa sahiptir:</span><span class="sxs-lookup"><span data-stu-id="d4376-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="d4376-416">Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:</span><span class="sxs-lookup"><span data-stu-id="d4376-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="d4376-417">`https://example.net`: Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="d4376-417">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="d4376-418">`https://www.example.com/foo.html`: Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="d4376-418">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="d4376-419">`http://example.com/foo.html`: Farklı düzen</span><span class="sxs-lookup"><span data-stu-id="d4376-419">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="d4376-420">`https://example.com:9000/foo.html`: Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="d4376-420">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="d4376-421">Internet Explorer, kaynakları karşılaştırırken bağlantı noktasını dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="d4376-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="d4376-422">Adlandırılmış ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="d4376-423">CORS ara yazılımı, çıkış noktaları arası istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="d4376-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="d4376-424">Aşağıdaki kod, belirtilen kaynağa sahip tüm uygulama için CORS 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="d4376-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="d4376-425">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="d4376-425">The preceding code:</span></span>

* <span data-ttu-id="d4376-426">İlke adını " \_ myallowspecifickaynaklarına" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="d4376-427">İlke adı rastgele olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="d4376-428"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS 'yi sağlayan genişletme yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="d4376-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="d4376-429"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar.</span><span class="sxs-lookup"><span data-stu-id="d4376-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="d4376-430">Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> .</span><span class="sxs-lookup"><span data-stu-id="d4376-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="d4376-431">Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="d4376-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="d4376-432"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:</span><span class="sxs-lookup"><span data-stu-id="d4376-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="d4376-433">Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="d4376-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="d4376-434"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemi aşağıdaki kodda gösterildiği gibi yöntemleri zincirleyebilir:</span><span class="sxs-lookup"><span data-stu-id="d4376-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="d4376-435">Not: URL sonunda eğik **not** çizgi ( `/` ) bulunmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="d4376-436">URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="d4376-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="d4376-437">Aşağıdaki kod, CORS ara yazılımı aracılığıyla CORS ilkelerini tüm uygulama uç noktalarına uygular:</span><span class="sxs-lookup"><span data-stu-id="d4376-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="d4376-438">Note: öğesinden `UseCors` önce çağrılmalıdır `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="d4376-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="d4376-439">CORS ilkesini sayfa/denetleyici/eylem düzeyinde uygulamak için bkz. [ Razor sayfalarda, denetleyicilerde ve eylem yöntemlerinde CORS 'yi etkinleştirme](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="d4376-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="d4376-440">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="d4376-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="d4376-441">CORS 'yi özniteliklerle etkinleştir</span><span class="sxs-lookup"><span data-stu-id="d4376-441">Enable CORS with attributes</span></span>

<span data-ttu-id="d4376-442">[ &lbrack; Enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliği, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="d4376-443">`[EnableCors]`Öznitelik, tüm bitiş noktaları yerine, seçilen bitiş noktaları IÇIN CORS 'yi mümkün.</span><span class="sxs-lookup"><span data-stu-id="d4376-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="d4376-444">`[EnableCors]`Varsayılan ilkeyi belirtmek ve `[EnableCors("{Policy String}")]` bir ilke belirlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="d4376-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="d4376-445">`[EnableCors]`Özniteliği öğesine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="d4376-445">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="d4376-446">Sayfasında`PageModel`</span><span class="sxs-lookup"><span data-stu-id="d4376-446"> Page `PageModel`</span></span>
* <span data-ttu-id="d4376-447">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="d4376-447">Controller</span></span>
* <span data-ttu-id="d4376-448">Denetleyici eylemi yöntemi</span><span class="sxs-lookup"><span data-stu-id="d4376-448">Controller action method</span></span>

<span data-ttu-id="d4376-449">Özniteliği ile denetleyici/sayfa-model/eylem 'e farklı ilkeler uygulayabilirsiniz `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="d4376-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="d4376-450">`[EnableCors]`Özniteliği bir denetleyiciler/sayfa modeli/eylem yöntemine uygulandığında ve bu işlem, ara yazılım 'NDA CORS 'yi etkinleştirmişse, **her iki ilke de** uygulanır.</span><span class="sxs-lookup"><span data-stu-id="d4376-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="d4376-451">İlkeleri **birleştirmediğimiz** için önerilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-451">We recommend **not** combining policies.</span></span> <span data-ttu-id="d4376-452">`[EnableCors]`Özniteliği veya ara yazılımı kullanın, **her ikisini birden**kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="d4376-452">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="d4376-453">Kullanırken `[EnableCors]` , varsayılan bir **not** ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="d4376-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="d4376-454">Aşağıdaki kod her bir yönteme farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="d4376-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="d4376-455">Aşağıdaki kod, bir CORS varsayılan ilkesi ve adlı bir ilke oluşturur `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="d4376-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="d4376-456">CORS 'yi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="d4376-456">Disable CORS</span></span>

<span data-ttu-id="d4376-457">[ &lbrack; Disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği denetleyici/sayfa MODELI/eylem için CORS 'yi devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="d4376-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="d4376-458">CORS ilke seçenekleri</span><span class="sxs-lookup"><span data-stu-id="d4376-458">CORS policy options</span></span>

<span data-ttu-id="d4376-459">Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="d4376-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="d4376-460">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="d4376-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="d4376-461">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="d4376-462">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="d4376-463">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="d4376-464">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="d4376-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="d4376-465">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="d4376-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="d4376-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="d4376-467">Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="d4376-468">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="d4376-468">Set the allowed origins</span></span>

<span data-ttu-id="d4376-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` .</span><span class="sxs-lookup"><span data-stu-id="d4376-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="d4376-470">`AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="d4376-471">`AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="d4376-472">Güvenli bir uygulama için, istemcinin sunucu kaynaklarına erişim yetkisi olması gerekiyorsa, kaynakların tam bir listesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="d4376-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="d4376-473">`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="d4376-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="d4376-474">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="d4376-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="d4376-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="d4376-476">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="d4376-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="d4376-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="d4376-478">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="d4376-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="d4376-479">Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="d4376-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="d4376-480">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="d4376-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="d4376-481">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-481">Set the allowed request headers</span></span>

<span data-ttu-id="d4376-482">Belirli başlıkların, *Yazar isteği üstbilgileri*ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:</span><span class="sxs-lookup"><span data-stu-id="d4376-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="d4376-483">Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="d4376-484">Bu ayar, ön kontrol isteklerini ve `Access-Control-Request-Headers` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="d4376-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="d4376-485">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="d4376-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="d4376-486">CORS ara yazılımı her zaman `Access-Control-Request-Headers` CorsPolicy. Headers ' de yapılandırılan değerlerden bağımsız olarak, ' deki dört üst bilgilerin gönderilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="d4376-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="d4376-487">Bu üst bilgi listesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="d4376-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="d4376-488">Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="d4376-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="d4376-489">CORS ara yazılımı, `Content-Language` her zaman beyaz listelenmiş olduğundan, aşağıdaki istek üstbilgisiyle bir ön kontrol isteğine başarıyla yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="d4376-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="d4376-490">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-490">Set the exposed response headers</span></span>

<span data-ttu-id="d4376-491">Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz.</span><span class="sxs-lookup"><span data-stu-id="d4376-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="d4376-492">Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="d4376-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="d4376-493">Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="d4376-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="d4376-494">CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="d4376-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="d4376-495">Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="d4376-496">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="d4376-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="d4376-497">Kimlik bilgileri CORS isteğinde özel işleme gerektirir.</span><span class="sxs-lookup"><span data-stu-id="d4376-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="d4376-498">Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez.</span><span class="sxs-lookup"><span data-stu-id="d4376-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="d4376-499">Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="d4376-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="d4376-500">Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="d4376-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="d4376-501">`XMLHttpRequest`Doğrudan kullanarak:</span><span class="sxs-lookup"><span data-stu-id="d4376-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="d4376-502">JQuery kullanarak:</span><span class="sxs-lookup"><span data-stu-id="d4376-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="d4376-503">[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:</span><span class="sxs-lookup"><span data-stu-id="d4376-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="d4376-504">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="d4376-504">The server must allow the credentials.</span></span> <span data-ttu-id="d4376-505">Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="d4376-506">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="d4376-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="d4376-507">Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="d4376-508">Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="d4376-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="d4376-509">Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="d4376-510">CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="d4376-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="d4376-511">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="d4376-511">Preflight requests</span></span>

<span data-ttu-id="d4376-512">Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir istek gönderir.</span><span class="sxs-lookup"><span data-stu-id="d4376-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="d4376-513">Bu isteğe bir *ön kontrol isteği*denir.</span><span class="sxs-lookup"><span data-stu-id="d4376-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="d4376-514">Aşağıdaki koşullar doğruysa tarayıcı, ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="d4376-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="d4376-515">İstek yöntemi al, HEAD veya POST.</span><span class="sxs-lookup"><span data-stu-id="d4376-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="d4376-516">Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="d4376-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="d4376-517">, `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="d4376-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="d4376-518">İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="d4376-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="d4376-519">CORS belirtimi, bu üst bilgiler *Yazar istek üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="d4376-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="d4376-520">Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="d4376-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="d4376-521">Aşağıda bir ön denetim isteğine örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="d4376-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="d4376-522">Uçuş öncesi isteği HTTP SEÇENEKLERI yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4376-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="d4376-523">İki özel üst bilgi içerir:</span><span class="sxs-lookup"><span data-stu-id="d4376-523">It includes two special headers:</span></span>

* <span data-ttu-id="d4376-524">`Access-Control-Request-Method`: Gerçek istek için kullanılacak HTTP yöntemi.</span><span class="sxs-lookup"><span data-stu-id="d4376-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="d4376-525">`Access-Control-Request-Headers`: Uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi.</span><span class="sxs-lookup"><span data-stu-id="d4376-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="d4376-526">Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="d4376-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="d4376-527">CORS uygun ilkeyle etkinleştirildiğinde ASP.NET Core, genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="d4376-528">[Ön kontrol istekleri için bkz. [HttpOptions] özniteliği](#pro).</span><span class="sxs-lookup"><span data-stu-id="d4376-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="d4376-529">CORS ön kontrol isteği `Access-Control-Request-Headers` , sunucuya gerçek istekle gönderilen üstbilgileri belirten bir üst bilgi içerebilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="d4376-530">Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="d4376-531">Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="d4376-532">Tarayıcılar, nasıl ayarlandıklarından tamamen tutarlı değildir `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="d4376-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="d4376-533">Üst bilgileri `"*"` (veya kullanımı) dışında bir şeye ayarlarsanız, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> desteklemek istediğiniz en az `Accept` , `Content-Type` , ve ve `Origin` Ek üstbilgileri dahil etmelisiniz.</span><span class="sxs-lookup"><span data-stu-id="d4376-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="d4376-534">Aşağıda, ön kontrol isteğine örnek bir yanıt verilmiştir (sunucunun isteğe izin verdiği varsayıldığında):</span><span class="sxs-lookup"><span data-stu-id="d4376-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="d4376-535">Yanıt, izin verilen `Access-Control-Allow-Methods` üst bilgileri listeleyen, izin verilen yöntemleri ve isteğe bağlı olarak bir üst bilgiyi listeleyen bir üst bilgi içerir `Access-Control-Allow-Headers` .</span><span class="sxs-lookup"><span data-stu-id="d4376-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="d4376-536">Ön kontrol isteği başarılı olursa, tarayıcı gerçek isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="d4376-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="d4376-537">Ön kontrol isteği reddedilirse, uygulama *200 ok* yanıtı döndürür ancak CORS üst bilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="d4376-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="d4376-538">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="d4376-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="d4376-539">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="d4376-539">Set the preflight expiration time</span></span>

<span data-ttu-id="d4376-540">`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="d4376-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="d4376-541">Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="d4376-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="d4376-542">CORS nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="d4376-542">How CORS works</span></span>

<span data-ttu-id="d4376-543">Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="d4376-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="d4376-544">CORS bir güvenlik özelliği **değil** .</span><span class="sxs-lookup"><span data-stu-id="d4376-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="d4376-545">CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="d4376-546">Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyalarını (XSS) engelliyor](xref:security/cross-site-scripting) ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="d4376-547">CORS 'nin izin vermesini sağlayan API 'niz daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="d4376-548">CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="d4376-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="d4376-549">Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir.</span><span class="sxs-lookup"><span data-stu-id="d4376-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="d4376-550">Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="d4376-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="d4376-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="d4376-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="d4376-552">Postman</span><span class="sxs-lookup"><span data-stu-id="d4376-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="d4376-553">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="d4376-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="d4376-554">Adres çubuğuna URL girerek bir Web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="d4376-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="d4376-555">Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="d4376-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="d4376-556">Tarayıcılar (CORS olmadan), çapraz kaynak istekleri yapamıyor.</span><span class="sxs-lookup"><span data-stu-id="d4376-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="d4376-557">CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="d4376-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="d4376-558">JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4376-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="d4376-559">Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="d4376-560">[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="d4376-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="d4376-561">Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="d4376-562">CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="d4376-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="d4376-563">Aşağıda, bir çapraz kaynak isteğine bir örnek verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="d4376-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="d4376-564">`Origin`Üst bilgi, isteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="d4376-565">`Origin`Üst bilgi gereklidir ve konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="d4376-566">Sunucu isteğe izin veriyorsa, `Access-Control-Allow-Origin` yanıttaki üstbilgiyi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="d4376-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="d4376-567">Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir veya joker değerdir `"*"` , yani herhangi bir kaynağa izin verilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="d4376-568">Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="d4376-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="d4376-569">Özellikle, tarayıcı isteğe izin vermez.</span><span class="sxs-lookup"><span data-stu-id="d4376-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="d4376-570">Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="d4376-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="d4376-571">Test CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-571">Test CORS</span></span>

<span data-ttu-id="d4376-572">CORS 'yi sınamak için:</span><span class="sxs-lookup"><span data-stu-id="d4376-572">To test CORS:</span></span>

1. <span data-ttu-id="d4376-573">[BIR API projesi oluşturun](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="d4376-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="d4376-574">Alternatif olarak, [örneği de indirebilirsiniz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="d4376-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="d4376-575">Bu belgedeki yaklaşımlardan birini kullanarak CORS 'yi etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="d4376-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="d4376-576">Örnek:</span><span class="sxs-lookup"><span data-stu-id="d4376-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="d4376-577">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="d4376-578">Web uygulaması projesi ( Razor Sayfalar veya Mvc) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="d4376-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="d4376-579">Örnek, Razor sayfaları kullanır.</span><span class="sxs-lookup"><span data-stu-id="d4376-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="d4376-580">Web uygulamasını, API projesiyle aynı çözümde oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="d4376-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="d4376-581">Aşağıdaki Vurgulanan kodu *Index. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="d4376-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="d4376-582">Yukarıdaki kodda, `url: 'https://<web app>.azurewebsites.net/api/values/1',` dağıtılan UYGULAMANıN URL 'siyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="d4376-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="d4376-583">API projesini dağıtın.</span><span class="sxs-lookup"><span data-stu-id="d4376-583">Deploy the API project.</span></span> <span data-ttu-id="d4376-584">Örneğin, [Azure 'a dağıtın](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="d4376-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="d4376-585">RazorMasaüstünden sayfaları veya MVC uygulamasını çalıştırın ve **Test** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="d4376-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="d4376-586">Hata iletilerini gözden geçirmek için F12 araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="d4376-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="d4376-587">Localhost kaynağını kaldırın `WithOrigins` ve uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="d4376-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="d4376-588">Alternatif olarak, istemci uygulamasını farklı bir bağlantı noktasıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d4376-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="d4376-589">Örneğin, Visual Studio 'dan çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="d4376-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="d4376-590">İstemci uygulamasıyla test edin.</span><span class="sxs-lookup"><span data-stu-id="d4376-590">Test with the client app.</span></span> <span data-ttu-id="d4376-591">CORS hataları bir hata döndürüyor, ancak hata iletisi JavaScript için kullanılabilir değil.</span><span class="sxs-lookup"><span data-stu-id="d4376-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="d4376-592">Hatayı görmek için F12 araçlarındaki konsol sekmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="d4376-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="d4376-593">Tarayıcıya bağlı olarak, aşağıdakine benzer bir hata alırsınız (F12 araçları konsolunda):</span><span class="sxs-lookup"><span data-stu-id="d4376-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="d4376-594">Microsoft Edge 'i kullanarak:</span><span class="sxs-lookup"><span data-stu-id="d4376-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="d4376-595">**SEC7120: [CORS] kaynak, `https://localhost:44375` `https://localhost:44375` ' deki çıkış noktaları için erişim-denetim-Izin-kaynak yanıt üstbilgisinde bulunamadı`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="d4376-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="d4376-596">Chrome kullanarak:</span><span class="sxs-lookup"><span data-stu-id="d4376-596">Using Chrome:</span></span>

     <span data-ttu-id="d4376-597">**Kaynak başında XMLHttpRequest erişimi `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok.**</span><span class="sxs-lookup"><span data-stu-id="d4376-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="d4376-598">CORS özellikli uç noktalar [Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="d4376-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="d4376-599">Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="d4376-600">İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :</span><span class="sxs-lookup"><span data-stu-id="d4376-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="d4376-601">CORS ara yazılımı için isteği işleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="d4376-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="d4376-602">CORS üstbilgileri yanıtta döndürülmedi.</span><span class="sxs-lookup"><span data-stu-id="d4376-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="d4376-603">IIS 'de CORS</span><span class="sxs-lookup"><span data-stu-id="d4376-603">CORS in IIS</span></span>

<span data-ttu-id="d4376-604">IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="d4376-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="d4376-605">Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="d4376-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4376-606">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="d4376-606">Additional resources</span></span>

* [<span data-ttu-id="d4376-607">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="d4376-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="d4376-608">IIS CORS modülünü kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="d4376-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
