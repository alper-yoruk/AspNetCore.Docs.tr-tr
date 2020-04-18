---
title: ASP.NET Çekirdekte Orijinler Arası İstekleri (CORS) etkinleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında, KORS'un çapraz orijin isteklerine izin vermek veya reddetmek için nasıl bir standart olduğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642695"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="74017-103">ASP.NET Çekirdekte Orijinler Arası İstekleri (CORS) etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="74017-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="74017-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="74017-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="74017-105">Bu makalede, bir ASP.NET Core uygulamasında CORS'in nasıl etkinleştirilen gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="74017-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="74017-106">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="74017-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="74017-107">Bu kısıtlamaya *aynı kaynak ilkesi*denir.</span><span class="sxs-lookup"><span data-stu-id="74017-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="74017-108">Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="74017-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="74017-109">Bazen, diğer sitelerin uygulamanızda çapraz orijin isteklerinde yer almasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74017-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="74017-110">Daha fazla bilgi için [Mozilla CORS makalesine](https://developer.mozilla.org/docs/Web/HTTP/CORS)bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="74017-111">[Çapraz Kaynak Paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="74017-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="74017-112">Bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="74017-113">Bir güvenlik özelliği **değildir,** CORS güvenliği rahatlatır.</span><span class="sxs-lookup"><span data-stu-id="74017-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="74017-114">BIR API CORS izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="74017-115">Daha fazla bilgi için [CORS'in nasıl çalıştığını](#how-cors)görün.</span><span class="sxs-lookup"><span data-stu-id="74017-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="74017-116">Sunucunun, diğerlerini reddederken bazı kişiler arası isteklere açıkça izin vermesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="74017-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="74017-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerden daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="74017-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="74017-118">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="74017-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="74017-119">Aynı köken</span><span class="sxs-lookup"><span data-stu-id="74017-119">Same origin</span></span>

<span data-ttu-id="74017-120">Aynı düzenleri, ana bilgisayarlar ve bağlantı noktaları[(RFC 6454)](https://tools.ietf.org/html/rfc6454)varsa, iki URL aynı kökene sahiptir.</span><span class="sxs-lookup"><span data-stu-id="74017-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="74017-121">Bu iki URL aynı kökene sahiptir:</span><span class="sxs-lookup"><span data-stu-id="74017-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="74017-122">Bu URL'lerin önceki iki URL'den farklı kökenleri vardır:</span><span class="sxs-lookup"><span data-stu-id="74017-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="74017-123">`https://example.net`&ndash; Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="74017-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="74017-124">`https://www.example.com/foo.html`&ndash; Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="74017-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="74017-125">`http://example.com/foo.html`&ndash; Farklı şema</span><span class="sxs-lookup"><span data-stu-id="74017-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="74017-126">`https://example.com:9000/foo.html`&ndash; Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="74017-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="74017-127">CORS'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="74017-127">Enable CORS</span></span>

<span data-ttu-id="74017-128">CORS'i etkinleştirmenin üç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="74017-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="74017-129">Adlandırılmış bir [ilke](#np) veya [varsayılan ilke](#dp)kullanarak ara yazılımda.</span><span class="sxs-lookup"><span data-stu-id="74017-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="74017-130">[Uç nokta yönlendirmeyi](#ecors)kullanma.</span><span class="sxs-lookup"><span data-stu-id="74017-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="74017-131">[[EnableCors]](#attr) özelliği ile.</span><span class="sxs-lookup"><span data-stu-id="74017-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="74017-132">[[EnableCors]](#attr) özniteliğinin adlandırılmış bir ilkeyle kullanılması, CORS'i destekleyen uç noktaları sınırlamada en iyi denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="74017-133">Her yaklaşım aşağıdaki bölümlerde ayrıntılı olarak açıklanır.</span><span class="sxs-lookup"><span data-stu-id="74017-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="74017-134">Adlı ilke ve middleware ile CORS</span><span class="sxs-lookup"><span data-stu-id="74017-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="74017-135">CORS Middleware, çapraz orijin isteklerini işler.</span><span class="sxs-lookup"><span data-stu-id="74017-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="74017-136">Aşağıdaki kod, belirtilen kökenlere sahip uygulamanın tüm uç noktalarına bir CORS ilkesi uygular:</span><span class="sxs-lookup"><span data-stu-id="74017-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="74017-137">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="74017-137">The preceding code:</span></span>

* <span data-ttu-id="74017-138">İlke adını `_myAllowSpecificOrigins`' a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="74017-139">İlke adı rasgele.</span><span class="sxs-lookup"><span data-stu-id="74017-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="74017-140">Uzantı <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> yöntemini çağırır ve `_myAllowSpecificOrigins` CORS ilkesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="74017-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="74017-141">`UseCors`CORS ara yazılımekler.</span><span class="sxs-lookup"><span data-stu-id="74017-141">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="74017-142">Çağrı sonra `UseCors` `UseRouting`yerleştirilmelidir, ancak `UseAuthorization`önce .</span><span class="sxs-lookup"><span data-stu-id="74017-142">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="74017-143">Daha fazla bilgi için [Middleware sırasına](xref:fundamentals/middleware/index#middleware-order)bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-143">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="74017-144"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile aramalar .</span><span class="sxs-lookup"><span data-stu-id="74017-144">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="74017-145">Lambda bir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nesne alır.</span><span class="sxs-lookup"><span data-stu-id="74017-145">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="74017-146">[Yapılandırma seçenekleri](#cors-policy-options), `WithOrigins`örneğin, bu makalede daha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="74017-146">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="74017-147">Tüm denetleyici `_myAllowSpecificOrigins` uç noktaları için CORS ilkesini etkinleştiri.</span><span class="sxs-lookup"><span data-stu-id="74017-147">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="74017-148">Belirli uç noktalara BIR CORS ilkesi uygulamak için [uç nokta yönlendirmesine](#ecors) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-148">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="74017-149">Uç nokta yönlendirmesi ile, CORS ara ware ve aramalar `UseRouting` arasında `UseEndpoints`yürütmek için ***yapılandırılmalıdır.***</span><span class="sxs-lookup"><span data-stu-id="74017-149">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="74017-150">Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#testc) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-150">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="74017-151">Yöntem <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> çağrısı, uygulamanın servis konteynerine CORS hizmetlerini ekler:</span><span class="sxs-lookup"><span data-stu-id="74017-151">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="74017-152">Daha fazla bilgi için bu belgedeki [CORS ilkesi seçeneklerine](#cpo) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-152">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="74017-153">Yöntemler, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> aşağıdaki kodda gösterildiği gibi zincirlenebilir:</span><span class="sxs-lookup"><span data-stu-id="74017-153">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="74017-154">Not: Belirtilen URL, sondaki eğik`/`çizgi () **içermemelidir.**</span><span class="sxs-lookup"><span data-stu-id="74017-154">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="74017-155">URL ile sonlandırma, `/`karşılaştırma `false` döndürür ve hiçbir üstbilgi döndürülür.</span><span class="sxs-lookup"><span data-stu-id="74017-155">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="74017-156">Varsayılan ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="74017-156">CORS with default policy and middleware</span></span>

<span data-ttu-id="74017-157">Aşağıdaki vurgulanan kod varsayılan CORS ilkesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="74017-157">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="74017-158">Önceki kod, varsayılan CORS ilkesini tüm denetleyici uç noktalarına uygular.</span><span class="sxs-lookup"><span data-stu-id="74017-158">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="74017-159">Uç nokta yönlendirme ile Kors'u etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="74017-159">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="74017-160">Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş [isteklerini](#apf) ***desteklemez.***</span><span class="sxs-lookup"><span data-stu-id="74017-160">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="74017-161">Daha fazla bilgi [için, bu GitHub sorunu](https://github.com/dotnet/aspnetcore/issues/20709) ve [test CORS uç noktası yönlendirme ve [HttpOptions] bakın.](#tcer)</span><span class="sxs-lookup"><span data-stu-id="74017-161">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="74017-162">Uç nokta yönlendirmeile CORS, uzantı yöntemleri <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> kümesi kullanılarak uç nokta başına etkinleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="74017-162">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="74017-163">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="74017-163">In the preceding code:</span></span>

* <span data-ttu-id="74017-164">`app.UseCors`CORS ara yazılımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-164">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="74017-165">Varsayılan ilke yapılandırılmadığından, `app.UseCors()` tek başına CORS'u etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="74017-165">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="74017-166">Ve `/echo` denetleyici uç noktaları belirtilen ilkeyi kullanarak orijinler arası isteklere izin verir.</span><span class="sxs-lookup"><span data-stu-id="74017-166">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="74017-167">Ve `/echo2` Razor Pages bitiş noktaları, varsayılan ilke belirtilmedi, çünkü çapraz başlangıç isteklerine izin ***vermez.***</span><span class="sxs-lookup"><span data-stu-id="74017-167">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="74017-168">[[Engelleyici Korse]](#dc) özelliği, uç nokta yönlendirme ile `RequireCors`etkinleştirilen CORS'leri ***devre*** dışı almaz.</span><span class="sxs-lookup"><span data-stu-id="74017-168">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="74017-169">Yukarıdakine benzer test koduyla ilgili talimatlar için [uç nokta yönlendirme stoyonu ve [HttpOptions] içeren Test CORS'e](#tcer) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-169">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="74017-170">Özniteliklerle CORS'i etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="74017-170">Enable CORS with attributes</span></span>

<span data-ttu-id="74017-171">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özelliği ile CORS etkinleştirme ve yalnızca CORS gerektiren uç noktalara adlandırılmış bir ilke uygulamak en iyi denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-171">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="74017-172">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliği, CORS'u küresel olarak uygulamak için bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-172">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="74017-173">Öznitelik, `[EnableCors]` tüm uç noktalar yerine seçili uç noktalar için CORS'e olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="74017-173">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="74017-174">`[EnableCors]`varsayılan ilkeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="74017-174">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="74017-175">`[EnableCors("{Policy String}")]`adlı bir ilke belirtir.</span><span class="sxs-lookup"><span data-stu-id="74017-175">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="74017-176">Öznitelik `[EnableCors]` uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="74017-176">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="74017-177">Jilet Sayfası`PageModel`</span><span class="sxs-lookup"><span data-stu-id="74017-177">Razor Page `PageModel`</span></span>
* <span data-ttu-id="74017-178">Denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="74017-178">Controller</span></span>
* <span data-ttu-id="74017-179">Denetleyici eylem yöntemi</span><span class="sxs-lookup"><span data-stu-id="74017-179">Controller action method</span></span>

<span data-ttu-id="74017-180">Öznitelik ile denetleyicilere, sayfa modellerine veya eylem `[EnableCors]` yöntemlerine farklı ilkeler uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-180">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="74017-181">Öznitelik `[EnableCors]` bir denetleyici, sayfa modeli veya eylem yöntemine uygulandığında ve ARA Yazılım'da CORS etkinleştirildiğinde, ***her iki*** ilke de uygulanır.</span><span class="sxs-lookup"><span data-stu-id="74017-181">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="74017-182">\*\*\*Politikaların birleştirilmesine karşı tavsiye ediyoruz. \*\*\*Her ***ikisini de aynı uygulamada değil, öznitelik veya ara yazılımı kullanın.*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="74017-182">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="74017-183">Aşağıdaki kod her yöntem için farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="74017-183">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="74017-184">Aşağıdaki kod iki CORS ilkeleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="74017-184">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="74017-185">CORS isteklerini sınırlandırma nın en iyi kontrolü için:</span><span class="sxs-lookup"><span data-stu-id="74017-185">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="74017-186">Adlandırılmış bir ilke ile kullanın. `[EnableCors("MyPolicy")]`</span><span class="sxs-lookup"><span data-stu-id="74017-186">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="74017-187">Varsayılan bir ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="74017-187">Don't define a default policy.</span></span>
* <span data-ttu-id="74017-188">Uç nokta [yönlendirmekullanmayın.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="74017-188">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="74017-189">Sonraki bölümdeki kod önceki listeyi karşılar.</span><span class="sxs-lookup"><span data-stu-id="74017-189">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="74017-190">Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#testc) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-190">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="74017-191">CORS'u devre dışı</span><span class="sxs-lookup"><span data-stu-id="74017-191">Disable CORS</span></span>

<span data-ttu-id="74017-192">[[Engelleyici Ler]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) [özniteliği, bitiş noktası yönlendirmesi](#ecors)ile etkinleştirilen CORS'leri devre ***dışı*** almaz.</span><span class="sxs-lookup"><span data-stu-id="74017-192">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="74017-193">Aşağıdaki kod CORS ilkesini `"MyPolicy"`tanımlar:</span><span class="sxs-lookup"><span data-stu-id="74017-193">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="74017-194">Aşağıdaki kod `GetValues2` eylem için CORS devre dışı kalmaktadır:</span><span class="sxs-lookup"><span data-stu-id="74017-194">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="74017-195">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="74017-195">The preceding code:</span></span>

* <span data-ttu-id="74017-196">[Uç nokta yönlendirmeile](#ecors)CORS'u etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="74017-196">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="74017-197">[Varsayılan BIR CORS ilkesi](#dp)tanımlamaz.</span><span class="sxs-lookup"><span data-stu-id="74017-197">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="74017-198">Denetleyiciiçin CORS ilkesini `"MyPolicy"` etkinleştirmek için [[EnableCors("MyPolicy")]](#attr) kullanır.</span><span class="sxs-lookup"><span data-stu-id="74017-198">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="74017-199">`GetValues2` Yöntem için CORS'u devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-199">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="74017-200">Yukarıdaki kodun sınanma talimatları için [Test CORS'a](#testc) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-200">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="74017-201">CORS politika seçenekleri</span><span class="sxs-lookup"><span data-stu-id="74017-201">CORS policy options</span></span>

<span data-ttu-id="74017-202">Bu bölümde, BIR CORS ilkesinde ayarlanabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="74017-202">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="74017-203">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-203">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="74017-204">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-204">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="74017-205">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-205">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="74017-206">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-206">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="74017-207">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="74017-207">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="74017-208">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-208">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="74017-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>olarak `Startup.ConfigureServices`adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="74017-209"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="74017-210">Bazı seçenekler için, [önce CORS nasıl çalışır](#how-cors) bölümünü okumak yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-210">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="74017-211">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-211">Set the allowed origins</span></span>

<span data-ttu-id="74017-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Herhangi bir şema (veya)`http` `https`ile tüm kökenlerden CORS isteklerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-212"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="74017-213">`AllowAnyOrigin`herhangi bir *web sitesi* uygulamaya çapraz orijin isteklerinde bulunabileceğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-213">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="74017-214">Belirtilmesi `AllowAnyOrigin` `AllowCredentials` ve güvenli olmayan bir yapılandırmadır ve site ler arası istek sahteciliğiyle sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-214">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="74017-215">Bir uygulama her iki yöntemle de yapılandırıldığında CORS hizmeti geçersiz bir CORS yanıtını döndürür.</span><span class="sxs-lookup"><span data-stu-id="74017-215">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="74017-216">`AllowAnyOrigin`ön kontrol isteklerini `Access-Control-Allow-Origin` ve üstbilgietkiler.</span><span class="sxs-lookup"><span data-stu-id="74017-216">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="74017-217">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-217">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="74017-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; İlkenin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> özelliğini, kaynağın izin verilip verilmediğini değerlendirirken, kökenlerin yapılandırılmış bir joker etki alanıyla eşleşmesine olanak tanıyan bir işlev olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="74017-219">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-219">Set the allowed HTTP methods</span></span>

<span data-ttu-id="74017-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="74017-220"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="74017-221">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="74017-221">Allows any HTTP method:</span></span>
* <span data-ttu-id="74017-222">Ön kontrol isteklerini `Access-Control-Allow-Methods` ve üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="74017-222">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="74017-223">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-223">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="74017-224">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-224">Set the allowed request headers</span></span>

<span data-ttu-id="74017-225">Belirli üstbilginin CORS isteğinde gönderilmesine izin vermek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> yazar [istek üstbilgisini](https://xhr.spec.whatwg.org/#request)arayın, arayın ve izin verilen üstbilgibelirtin:</span><span class="sxs-lookup"><span data-stu-id="74017-225">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="74017-226">Tüm [yazar istek üstbilgilerine](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için , arayın: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="74017-226">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="74017-227">`AllowAnyHeader`ön kontrol isteklerini ve [Erişim-Kontrol-İstek-Üstbilgi](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) üstbilgisini etkiler.</span><span class="sxs-lookup"><span data-stu-id="74017-227">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="74017-228">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-228">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="74017-229">Cors Middleware ilkesi tarafından `WithHeaders` belirtilen belirli üstbilgilerle eşleşerek, `Access-Control-Request-Headers` yalnızca gönderilen üstbilgiler `WithHeaders`üstbilgilerle tam olarak eşleştiğinde mümkündür.</span><span class="sxs-lookup"><span data-stu-id="74017-229">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="74017-230">Örneğin, aşağıdaki gibi yapılandırılan bir uygulamayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="74017-230">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="74017-231">([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) listede yer olmadığı `Content-Language` için CORS Middleware aşağıdaki istek üstbilgisi ile ön kontrol isteğini `WithHeaders`reddeder:</span><span class="sxs-lookup"><span data-stu-id="74017-231">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="74017-232">Uygulama *200 Ok* yanıtı döndürür, ancak CORS üstbilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="74017-232">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="74017-233">Bu nedenle, tarayıcı çapraz kaynak isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="74017-233">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="74017-234">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-234">Set the exposed response headers</span></span>

<span data-ttu-id="74017-235">Varsayılan olarak, tarayıcı tüm yanıt üstbilgisini uygulamaya maruz bırakmaz.</span><span class="sxs-lookup"><span data-stu-id="74017-235">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="74017-236">Daha fazla bilgi için [W3C Cross-Origin Kaynak Paylaşımı (Terminoloji): Basit Yanıt Üstbilgisi'ne](https://www.w3.org/TR/cors/#simple-response-header)bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-236">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="74017-237">Varsayılan olarak kullanılabilir yanıt üstbilgi:</span><span class="sxs-lookup"><span data-stu-id="74017-237">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="74017-238">CORS belirtimi bu üstbilgi *basit yanıt üstbilgi çağırır.*</span><span class="sxs-lookup"><span data-stu-id="74017-238">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="74017-239">Diğer üstbilgileri uygulamaiçin kullanılabilir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hale getirmek için aşağıdakileri arayın:</span><span class="sxs-lookup"><span data-stu-id="74017-239">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="74017-240">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="74017-240">Credentials in cross-origin requests</span></span>

<span data-ttu-id="74017-241">Kimlik bilgileri, CORS isteğinde özel kullanım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="74017-241">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="74017-242">Varsayılan olarak, tarayıcı bir çapraz kaynak isteği ile kimlik bilgileri göndermez.</span><span class="sxs-lookup"><span data-stu-id="74017-242">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="74017-243">Kimlik bilgileri çerezleri ve HTTP kimlik doğrulama şemalarını içerir.</span><span class="sxs-lookup"><span data-stu-id="74017-243">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="74017-244">Bir çapraz kaynak isteği ile kimlik bilgileri `XMLHttpRequest.withCredentials` göndermek `true`için, istemci nin .</span><span class="sxs-lookup"><span data-stu-id="74017-244">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="74017-245">Doğrudan `XMLHttpRequest` kullanma:</span><span class="sxs-lookup"><span data-stu-id="74017-245">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="74017-246">jQuery kullanma:</span><span class="sxs-lookup"><span data-stu-id="74017-246">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="74017-247">Getir [API'sini](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanma :</span><span class="sxs-lookup"><span data-stu-id="74017-247">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="74017-248">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="74017-248">The server must allow the credentials.</span></span> <span data-ttu-id="74017-249">Orijinler arası kimlik <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>bilgilerine izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="74017-249">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="74017-250">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgilerine izin verdiğini söyleyen bir üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="74017-250">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="74017-251">Tarayıcı kimlik bilgileri gönderir, ancak yanıt geçerli `Access-Control-Allow-Credentials` bir üstbilgi içermiyorsa, tarayıcı uygulamaya yanıtı açıklamaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="74017-251">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="74017-252">Orjinal kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="74017-252">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="74017-253">Başka bir etki alanında bulunan bir web sitesi, kullanıcının bilgisi olmadan kullanıcı adına oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-253">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="74017-254">CORS belirtimi, üstbilgi `"*"` varsa(tüm kökenlerin) kaynağını ayarlamanın `Access-Control-Allow-Credentials` geçersiz olduğunu da belirtir.</span><span class="sxs-lookup"><span data-stu-id="74017-254">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="74017-255">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="74017-255">Preflight requests</span></span>

<span data-ttu-id="74017-256">Bazı CORS istekleri için tarayıcı, gerçek isteği yapmadan önce ek bir [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="74017-256">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="74017-257">Bu istek [bir ön kontrol isteği](https://developer.mozilla.org/docs/Glossary/Preflight_request)olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="74017-257">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="74017-258">Aşağıdaki ***tüm*** koşullar doğruysa, tarayıcı ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="74017-258">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="74017-259">İstek yöntemi GET, HEAD veya POST'dur.</span><span class="sxs-lookup"><span data-stu-id="74017-259">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="74017-260">Uygulama, istek `Accept`üstbilgisini , , `Accept-Language` `Content-Language`, `Content-Type`veya `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="74017-260">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="74017-261">Üstbilgi, `Content-Type` küme, aşağıdaki değerlerden birine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="74017-261">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="74017-262">İstemci isteği için ayarlanan istek üstbilgilerine ilişkin kural, `setRequestHeader` `XMLHttpRequest` uygulamanın nesneyi çağırarak ayarladığını üstbilgi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="74017-262">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="74017-263">CORS belirtimi bu üstbilgi [yazar istek üstbilgi](https://www.w3.org/TR/cors/#author-request-headers)çağırır.</span><span class="sxs-lookup"><span data-stu-id="74017-263">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="74017-264">Kural, tarayıcının ayarlayabileceği `User-Agent`, , `Host`veya `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="74017-264">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="74017-265">Aşağıda, bu belgenin [Test CORS](#testc) bölümündeki **[Test Yap]** düğmesinden yapılan ön kontrol isteğine benzer bir örnek yanıt verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="74017-265">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="74017-266">Ön kontrol isteği [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="74017-266">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="74017-267">Aşağıdaki üstbilgi içerebilir:</span><span class="sxs-lookup"><span data-stu-id="74017-267">It may include the following headers:</span></span>

* <span data-ttu-id="74017-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Gerçek istek için kullanılacak HTTP yöntemi.</span><span class="sxs-lookup"><span data-stu-id="74017-268">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="74017-269">[Erişim-Denetim-İstek-Üstbilgi](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Uygulamanın gerçek istek üzerine ayarladığının istek üstbilgilerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="74017-269">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="74017-270">Daha önce belirtildiği gibi, `User-Agent`bu, tarayıcının belirlediği üstleri içermez.</span><span class="sxs-lookup"><span data-stu-id="74017-270">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="74017-271">Erişim-Denetim-İzin Verme-Yöntemler</span><span class="sxs-lookup"><span data-stu-id="74017-271">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="74017-272">Ön kontrol isteği reddedilirse, uygulama bir `200 OK` yanıt verir, ancak CORS üstbilgisini ayarlamaz.</span><span class="sxs-lookup"><span data-stu-id="74017-272">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="74017-273">Bu nedenle, tarayıcı çapraz kaynak isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="74017-273">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="74017-274">Reddedilen ön kontrol isteği örneği için, bu belgenin [Test CORS](#testc) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-274">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="74017-275">Konsol uygulaması, F12 araçlarını kullanarak tarayıcıya bağlı olarak aşağıdakilerden birine benzer bir hata gösterir:</span><span class="sxs-lookup"><span data-stu-id="74017-275">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="74017-276">Firefox: Cross-Origin İstek Engellendi: Aynı Başlangıç Politikası uzak `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`kaynağı okuma ya da izin vermiyor.</span><span class="sxs-lookup"><span data-stu-id="74017-276">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="74017-277">(Sebep: CORS isteği başarılı olmadı).</span><span class="sxs-lookup"><span data-stu-id="74017-277">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="74017-278">Daha Fazla Bilgi Edinin</span><span class="sxs-lookup"><span data-stu-id="74017-278">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="74017-279">Krom bazlı: ' 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5from origin 'https://cors3.azurewebsites.net' 'eve erişim CORS ilkesi tarafından engellendi: Ön kontrol isteğine yanıt erişim denetiminden geçmiyor: İstenen kaynakta 'Access-Control-Allow-Allow-Origin' başlığı bulunmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="74017-279">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="74017-280">İhtiyaçlarınıza opak bir yanıt yanıt veriyorsa, CORS devre dışı bırakılmış kaynağı almak için istek modunu 'kors'a göre ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="74017-280">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="74017-281">Belirli üstbilgi için: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="74017-281">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="74017-282">Tüm [yazar istek üstbilgilerine](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için , arayın: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="74017-282">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="74017-283">Tarayıcılar, ayarlarken `Access-Control-Request-Headers`tutarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-283">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="74017-284">Eğer ikisinden biri:</span><span class="sxs-lookup"><span data-stu-id="74017-284">If either:</span></span>

* <span data-ttu-id="74017-285">Üstbilgi, başka bir şeye ayarlanır`"*"`</span><span class="sxs-lookup"><span data-stu-id="74017-285">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="74017-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>denir: En az `Accept` `Content-Type`, `Origin`, ve , artı desteklemek istediğiniz herhangi bir özel üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="74017-286"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="74017-287">Otomatik ön kontrol istek kodu</span><span class="sxs-lookup"><span data-stu-id="74017-287">Automatic preflight request code</span></span>

<span data-ttu-id="74017-288">CORS ilkesi uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="74017-288">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="74017-289">Global olarak `app.UseCors` arayarak `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="74017-289">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="74017-290">Özniteliği `[EnableCors]` kullanarak.</span><span class="sxs-lookup"><span data-stu-id="74017-290">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="74017-291">ASP.NET Core, uçuş öncesi OPTIONS isteğine yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="74017-291">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="74017-292">Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş isteklerini ***desteklemez.***</span><span class="sxs-lookup"><span data-stu-id="74017-292">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="74017-293">Bu belgenin [Test CORS](#testc) bölümü bu davranışı gösterir.</span><span class="sxs-lookup"><span data-stu-id="74017-293">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="74017-294">[HttpOptions] ön kontrol istekleri için öznitelik</span><span class="sxs-lookup"><span data-stu-id="74017-294">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="74017-295">CORS uygun ilke ile etkinleştirildiğinde, ASP.NET Core genellikle CORS uçuş öncesi isteklerine otomatik olarak yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="74017-295">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="74017-296">Bazı senaryolarda durum böyle olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-296">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="74017-297">Örneğin, [uç nokta yönlendirme ile CORS](#ecors)kullanarak.</span><span class="sxs-lookup"><span data-stu-id="74017-297">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="74017-298">Aşağıdaki kod, OPTIONS istekleri için uç noktaları oluşturmak için [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="74017-298">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="74017-299">Yukarıdaki kodun sınanma yönergeleri için [uç nokta yönlendirme ve [HttpOptions] içeren Test CORS'e](#tcer) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-299">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="74017-300">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-300">Set the preflight expiration time</span></span>

<span data-ttu-id="74017-301">Üstbilgi, `Access-Control-Max-Age` ön kontrol isteğine verilen yanıtın ne kadar süreyle önbelleğe alınabileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="74017-301">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="74017-302">Bu üstbilgi ayarlamak <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>için:</span><span class="sxs-lookup"><span data-stu-id="74017-302">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="74017-303">CORS nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="74017-303">How CORS works</span></span>

<span data-ttu-id="74017-304">Bu bölümde, HTTP iletileri düzeyinde bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğine neler olduğu açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="74017-304">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="74017-305">CORS bir güvenlik özelliği **değildir.**</span><span class="sxs-lookup"><span data-stu-id="74017-305">CORS is **not** a security feature.</span></span> <span data-ttu-id="74017-306">CORS, bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-306">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="74017-307">Örneğin, kötü niyetli bir aktör sitenize karşı [Site Arası Komut Dosyası (XSS)](xref:security/cross-site-scripting) kullanabilir ve cors özellikli sitesine bilgi çalmak için bir çapraz site isteği yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-307">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="74017-308">BIR API CORS izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-308">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="74017-309">CORS'i uygulamak istemciye (tarayıcıya) kalmış.</span><span class="sxs-lookup"><span data-stu-id="74017-309">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="74017-310">Sunucu isteği yürütür ve yanıtı döndürür, bir hata döndürür ve yanıtı engeller istemcisidir.</span><span class="sxs-lookup"><span data-stu-id="74017-310">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="74017-311">Örneğin, aşağıdaki araçlardan herhangi biri sunucu yanıtını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="74017-311">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="74017-312">Fiddler</span><span class="sxs-lookup"><span data-stu-id="74017-312">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="74017-313">Postman</span><span class="sxs-lookup"><span data-stu-id="74017-313">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="74017-314">.NET Https://yayla</span><span class="sxs-lookup"><span data-stu-id="74017-314">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="74017-315">Adres çubuğuna URL girerek bir web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="74017-315">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="74017-316">Bu, bir sunucunun tarayıcıların aksi takdirde yasaklanacak bir çapraz kökenli [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteğini yürütmesine izin vermenin bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="74017-316">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="74017-317">CORS'siz tarayıcılar çapraz kökenli isteklerde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-317">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="74017-318">CORS önce, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) bu kısıtlamayı atlatmak için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="74017-318">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="74017-319">JSONP XHR kullanmaz, yanıtı `<script>` almak için etiketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="74017-319">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="74017-320">Komut dosyalarıçapraz kökenli yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-320">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="74017-321">[CORS belirtimi,](https://www.w3.org/TR/cors/) orijinler arası istekleri etkinleştiren birkaç yeni HTTP üstbilgisini tanıttı.</span><span class="sxs-lookup"><span data-stu-id="74017-321">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="74017-322">Bir tarayıcı CORS'i destekliyorsa, bu başlıkları çapraz başlangıç istekleri için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-322">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="74017-323">CORS'i etkinleştirmek için özel JavaScript kodu gerekmez.</span><span class="sxs-lookup"><span data-stu-id="74017-323">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="74017-324">Dağıtılan [örnekteki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [PUT test düğmesi](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="74017-324">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="74017-325">Aşağıda, [Değerler](https://cors3.azurewebsites.net/) test düğmesinden `https://cors1.azurewebsites.net/api/values`' e bir çapraz kaynak isteği örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="74017-325">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="74017-326">Üstbilgi: `Origin`</span><span class="sxs-lookup"><span data-stu-id="74017-326">The `Origin` header:</span></span>

* <span data-ttu-id="74017-327">İstekte bulunduran sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-327">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="74017-328">Gereklidir ve ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-328">Is required and must be different from the host.</span></span>

<span data-ttu-id="74017-329">**Genel başlıklar**</span><span class="sxs-lookup"><span data-stu-id="74017-329">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="74017-330">**Yanıt üstleri**</span><span class="sxs-lookup"><span data-stu-id="74017-330">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="74017-331">**İstek üstbilgi**</span><span class="sxs-lookup"><span data-stu-id="74017-331">**Request headers**</span></span>

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

<span data-ttu-id="74017-332">İsteklerde, `OPTIONS` sunucu yanıt **üstbilgisini** `Access-Control-Allow-Origin: {allowed origin}` yanıt olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-332">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="74017-333">Örneğin, dağıtılan [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Sil [EnableCors]](https://cors1.azurewebsites.net/test?number=2) düğmesi `OPTIONS` isteği aşağıdaki üstbilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="74017-333">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="74017-334">**Genel başlıklar**</span><span class="sxs-lookup"><span data-stu-id="74017-334">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="74017-335">**Yanıt üstleri**</span><span class="sxs-lookup"><span data-stu-id="74017-335">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="74017-336">**İstek üstbilgi**</span><span class="sxs-lookup"><span data-stu-id="74017-336">**Request headers**</span></span>

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

<span data-ttu-id="74017-337">Önceki Yanıt **üstbilgisinde,** sunucu yanıtta [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) üstbilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-337">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="74017-338">Bu `https://cors1.azurewebsites.net` üstbilginin değeri `Origin` istekteki üstbilgiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="74017-338">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="74017-339"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> Çağrıldığında, `Access-Control-Allow-Origin: *`joker karakter değeri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="74017-339">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="74017-340">`AllowAnyOrigin`herhangi bir menşe sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-340">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="74017-341">Yanıt `Access-Control-Allow-Origin` üstbilgi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="74017-341">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="74017-342">Özellikle, tarayıcı isteği izin verir.</span><span class="sxs-lookup"><span data-stu-id="74017-342">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="74017-343">Sunucu başarılı bir yanıt döndürse bile, tarayıcı yanıtı istemci uygulamasına sunmaz.</span><span class="sxs-lookup"><span data-stu-id="74017-343">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="74017-344">SEÇENEKLER isteklerini görüntüleme</span><span class="sxs-lookup"><span data-stu-id="74017-344">Display OPTIONS requests</span></span>

<span data-ttu-id="74017-345">Varsayılan olarak, Chrome ve Edge tarayıcıları F12 araçlarının ağ sekmesinde OPTIONS isteklerini göstermez.</span><span class="sxs-lookup"><span data-stu-id="74017-345">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="74017-346">Bu tarayıcılarda OPTIONS isteklerini görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="74017-346">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="74017-347">`chrome://flags/#out-of-blink-cors` veya `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="74017-347">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="74017-348">bayrağı devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-348">disable the flag.</span></span>
* <span data-ttu-id="74017-349">Yeni -den başlatın.</span><span class="sxs-lookup"><span data-stu-id="74017-349">restart.</span></span>

<span data-ttu-id="74017-350">Firefox varsayılan olarak OPTIONS isteklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="74017-350">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="74017-351">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="74017-351">CORS in IIS</span></span>

<span data-ttu-id="74017-352">IIS'ye dağıtılırken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS'in Windows Kimlik Doğrulama'dan önce çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="74017-352">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="74017-353">Bu senaryoyu desteklemek için [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmesi ve yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="74017-353">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="74017-354">Test CORS</span><span class="sxs-lookup"><span data-stu-id="74017-354">Test CORS</span></span>

<span data-ttu-id="74017-355">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) CORS test etmek için kodu vardır.</span><span class="sxs-lookup"><span data-stu-id="74017-355">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="74017-356">[Nasıl indirilir bakın.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="74017-356">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="74017-357">Örnek Razor Pages eklenen bir API projesidir:</span><span class="sxs-lookup"><span data-stu-id="74017-357">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="74017-358">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)benzer bir örnek uygulamayı test etmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-358">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="74017-359">Aşağıdaki `ValuesController` sınama için uç noktaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="74017-359">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="74017-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet paketi tarafından sağlanır ve rota bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="74017-360">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="74017-361">Aşağıdaki yaklaşımlardan birini kullanarak önceki örnek kodu test edin:</span><span class="sxs-lookup"><span data-stu-id="74017-361">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="74017-362">Dağıtılan örnek uygulamayı ' [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)da kullanın.</span><span class="sxs-lookup"><span data-stu-id="74017-362">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="74017-363">Örneği indirmenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="74017-363">There is no need to download the sample.</span></span>
* <span data-ttu-id="74017-364">Örneği varsayılan `dotnet run` URL'sini kullanarak `https://localhost:5001`çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="74017-364">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="74017-365">Bir URL için 44398 olarak ayarlanmış bağlantı noktası `https://localhost:44398`ile Visual Studio örnek çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="74017-365">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="74017-366">F12 araçları ile bir tarayıcı kullanma:</span><span class="sxs-lookup"><span data-stu-id="74017-366">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="74017-367">**Değerler** düğmesini seçin ve **Ağ** sekmesindeki üstbilgiincelemesi.</span><span class="sxs-lookup"><span data-stu-id="74017-367">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="74017-368">PUT **test** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="74017-368">Select the **PUT test** button.</span></span> <span data-ttu-id="74017-369">SEÇENEKLER isteğini görüntülerken verilen talimatlar için SEÇENEKLER'i görüntüleme [isteklerine](#options) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-369">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="74017-370">**PUT testi** iki istek, bir OPTIONS ön uçuş isteği ve PUT isteği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="74017-370">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="74017-371">Başarısız **`GetValues2 [DisableCors]`** bir CORS isteğini tetiklemek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="74017-371">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="74017-372">Belgede belirtildiği gibi, yanıt 200 başarı döndürür, ancak CORS isteği yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="74017-372">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="74017-373">CORS hatasını görmek için **Konsol** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="74017-373">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="74017-374">Tarayıcıya bağlı olarak, aşağıdakilere benzer bir hata görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="74017-374">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="74017-375">`'https://cors1.azurewebsites.net/api/values/GetValues2'` Orijinden `'https://cors3.azurewebsites.net'` erişim CORS ilkesi tarafından engellendi: İstenen kaynakta 'Access-Control-Allow-Origin' başlığı bulunmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="74017-375">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="74017-376">İhtiyaçlarınıza opak bir yanıt yanıt veriyorsa, CORS devre dışı bırakılmış kaynağı almak için istek modunu 'kors'a göre ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="74017-376">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="74017-377">CORS özellikli uç [noktalar, curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)veya [Postacı](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-377">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="74017-378">Bir araç kullanırken, `Origin` üstbilgi tarafından belirtilen isteğin kaynağı, isteği alan ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-378">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="74017-379">Üstbilginin değerine *cross-origin* `Origin` göre istek çapraz kökenli değilse:</span><span class="sxs-lookup"><span data-stu-id="74017-379">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="74017-380">CORS Middleware'in isteği işlemesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="74017-380">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="74017-381">CORS başlıkları yanıt olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="74017-381">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="74017-382">Aşağıdaki komut, `curl` bir OPTIONS isteğini bilgiyle birlikte vermek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="74017-382">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="74017-383">Uç nokta yönlendirme ile TEST CORS ve [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="74017-383">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="74017-384">Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş [isteklerini](#apf) ***desteklemez.***</span><span class="sxs-lookup"><span data-stu-id="74017-384">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="74017-385">[CORS'i etkinleştirmek için uç nokta yönlendirmeyi](#ecors)kullanan aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="74017-385">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="74017-386">Aşağıdaki `TodoItems1Controller` sınama için uç noktaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="74017-386">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="74017-387">Dağıtılan [örneğin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [test sayfasından](https://cors1.azurewebsites.net/test?number=1) önceki kodu test edin.</span><span class="sxs-lookup"><span data-stu-id="74017-387">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="74017-388">**[EnableCors]** ve **GET [EnableCors] düğmeleri** başarılı olur, `[EnableCors]` çünkü uç noktalar uçuş öncesi isteklere sahip ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="74017-388">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="74017-389">Diğer uç noktalar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="74017-389">The other endpoints fails.</span></span> <span data-ttu-id="74017-390">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) gönderdiğinden **GET** düğmesi başarısız olur:</span><span class="sxs-lookup"><span data-stu-id="74017-390">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="74017-391">Aşağıdakibenzer `TodoItems2Controller` uç noktaları sağlar, ancak OPTIONS isteklerini yanıtlamak için açık kod içerir:</span><span class="sxs-lookup"><span data-stu-id="74017-391">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="74017-392">Dağıtılan örneğin [test sayfasından](https://cors1.azurewebsites.net/test?number=2) önceki kodu test edin.</span><span class="sxs-lookup"><span data-stu-id="74017-392">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="74017-393">**Denetleyici** açılır listesinde Ön **Kontrol'i** seçin ve ardından **Denetleyiciyi Ayarlayın.**</span><span class="sxs-lookup"><span data-stu-id="74017-393">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="74017-394">`TodoItems2Controller` Uç noktalara yapılan tüm CORS çağrıları başarılı dır.</span><span class="sxs-lookup"><span data-stu-id="74017-394">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74017-395">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="74017-395">Additional resources</span></span>

* [<span data-ttu-id="74017-396">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="74017-396">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="74017-397">IIS CORS modülü ile başlarken</span><span class="sxs-lookup"><span data-stu-id="74017-397">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="74017-398">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="74017-398">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="74017-399">Bu makalede, bir ASP.NET Core uygulamasında CORS'in nasıl etkinleştirilen gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="74017-399">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="74017-400">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="74017-400">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="74017-401">Bu kısıtlamaya *aynı kaynak ilkesi*denir.</span><span class="sxs-lookup"><span data-stu-id="74017-401">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="74017-402">Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="74017-402">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="74017-403">Bazen, diğer sitelerin uygulamanızda çapraz orijin isteklerinde yer almasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74017-403">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="74017-404">Daha fazla bilgi için [Mozilla CORS makalesine](https://developer.mozilla.org/docs/Web/HTTP/CORS)bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-404">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="74017-405">[Çapraz Kaynak Paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="74017-405">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="74017-406">Bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-406">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="74017-407">Bir güvenlik özelliği **değildir,** CORS güvenliği rahatlatır.</span><span class="sxs-lookup"><span data-stu-id="74017-407">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="74017-408">BIR API CORS izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-408">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="74017-409">Daha fazla bilgi için [CORS'in nasıl çalıştığını](#how-cors)görün.</span><span class="sxs-lookup"><span data-stu-id="74017-409">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="74017-410">Sunucunun, diğerlerini reddederken bazı kişiler arası isteklere açıkça izin vermesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="74017-410">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="74017-411">[JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerden daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="74017-411">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="74017-412">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="74017-412">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="74017-413">Aynı köken</span><span class="sxs-lookup"><span data-stu-id="74017-413">Same origin</span></span>

<span data-ttu-id="74017-414">Aynı düzenleri, ana bilgisayarlar ve bağlantı noktaları[(RFC 6454)](https://tools.ietf.org/html/rfc6454)varsa, iki URL aynı kökene sahiptir.</span><span class="sxs-lookup"><span data-stu-id="74017-414">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="74017-415">Bu iki URL aynı kökene sahiptir:</span><span class="sxs-lookup"><span data-stu-id="74017-415">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="74017-416">Bu URL'lerin önceki iki URL'den farklı kökenleri vardır:</span><span class="sxs-lookup"><span data-stu-id="74017-416">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="74017-417">`https://example.net`&ndash; Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="74017-417">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="74017-418">`https://www.example.com/foo.html`&ndash; Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="74017-418">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="74017-419">`http://example.com/foo.html`&ndash; Farklı şema</span><span class="sxs-lookup"><span data-stu-id="74017-419">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="74017-420">`https://example.com:9000/foo.html`&ndash; Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="74017-420">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="74017-421">Internet Explorer, kaynaklarını karşılaştırırken bağlantı noktasını dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="74017-421">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="74017-422">Adlı ilke ve middleware ile CORS</span><span class="sxs-lookup"><span data-stu-id="74017-422">CORS with named policy and middleware</span></span>

<span data-ttu-id="74017-423">CORS Middleware, çapraz orijin isteklerini işler.</span><span class="sxs-lookup"><span data-stu-id="74017-423">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="74017-424">Aşağıdaki kod, belirtilen menşee sahip tüm uygulama için CORS sağlar:</span><span class="sxs-lookup"><span data-stu-id="74017-424">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="74017-425">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="74017-425">The preceding code:</span></span>

* <span data-ttu-id="74017-426">İlke adını "myAllowSpecificOrigins"\_olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-426">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="74017-427">İlke adı rasgele.</span><span class="sxs-lookup"><span data-stu-id="74017-427">The policy name is arbitrary.</span></span>
* <span data-ttu-id="74017-428">CORS'u sağlayan uzatma yöntemini <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> çağırır.</span><span class="sxs-lookup"><span data-stu-id="74017-428">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="74017-429"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile aramalar .</span><span class="sxs-lookup"><span data-stu-id="74017-429">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="74017-430">Lambda bir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nesne alır.</span><span class="sxs-lookup"><span data-stu-id="74017-430">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="74017-431">[Yapılandırma seçenekleri](#cors-policy-options), `WithOrigins`örneğin, bu makalede daha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="74017-431">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="74017-432">Yöntem <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> çağrısı, uygulamanın servis konteynerine CORS hizmetlerini ekler:</span><span class="sxs-lookup"><span data-stu-id="74017-432">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="74017-433">Daha fazla bilgi için bu belgedeki [CORS ilke seçeneklerine](#cpo) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-433">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="74017-434">Yöntem, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> aşağıdaki kodda gösterildiği gibi yöntemleri zincirleyebilir:</span><span class="sxs-lookup"><span data-stu-id="74017-434">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="74017-435">Not: URL' de sondaki eğik çizgi ()`/` **içermemelidir.**</span><span class="sxs-lookup"><span data-stu-id="74017-435">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="74017-436">URL ile sonlandırma, `/`karşılaştırma `false` döndürür ve hiçbir üstbilgi döndürülür.</span><span class="sxs-lookup"><span data-stu-id="74017-436">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="74017-437">Aşağıdaki kod, CORS Middleware aracılığıyla tüm uygulama uç noktalarına CORS ilkelerini uygular:</span><span class="sxs-lookup"><span data-stu-id="74017-437">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="74017-438">Not: `UseCors` önce `UseMvc`çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-438">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="74017-439">Bkz. Sayfa/denetleyici/eylem düzeyinde CORS ilkesini uygulamak için [Razor Pages, denetleyiciler ve eylem yöntemlerinde CORS'i etkinleştirin.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="74017-439">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="74017-440">Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#test) bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-440">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="74017-441">Özniteliklerle CORS'i etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="74017-441">Enable CORS with attributes</span></span>

<span data-ttu-id="74017-442">[EnableCors&rbrack; özniteliği, CORS'u küresel olarak uygulamak için bir alternatif sağlar. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="74017-442">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="74017-443">Öznitelik, `[EnableCors]` tüm uç noktalar yerine seçilen uç noktalar için CORS'e olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-443">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="74017-444">Varsayılan `[EnableCors]` ilkeyi belirtmek `[EnableCors("{Policy String}")]` ve bir ilke belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="74017-444">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="74017-445">Öznitelik `[EnableCors]` uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="74017-445">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="74017-446">Jilet Sayfası`PageModel`</span><span class="sxs-lookup"><span data-stu-id="74017-446">Razor Page `PageModel`</span></span>
* <span data-ttu-id="74017-447">Denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="74017-447">Controller</span></span>
* <span data-ttu-id="74017-448">Denetleyici eylem yöntemi</span><span class="sxs-lookup"><span data-stu-id="74017-448">Controller action method</span></span>

<span data-ttu-id="74017-449">Öznitelik ile `[EnableCors]` denetleyici/sayfa modeli/eylem için farklı ilkeler uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74017-449">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="74017-450">Öznitelik `[EnableCors]` denetleyicilere/sayfa modeli/eylem yöntemine uygulandığında ve ara yazılımda CORS etkinleştirildiğinde, ***her iki*** ilke de uygulanır.</span><span class="sxs-lookup"><span data-stu-id="74017-450">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="74017-451">İlkeleri ***birleştirmemenizi*** öneririz.</span><span class="sxs-lookup"><span data-stu-id="74017-451">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="74017-452">Öznitelik veya ara yazılım kullanın, \***her ikisi de değil**. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="74017-452">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="74017-453">Kullanırken, `[EnableCors]`varsayılan bir ilke **tanımlamayın.**</span><span class="sxs-lookup"><span data-stu-id="74017-453">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="74017-454">Aşağıdaki kod her yöntem için farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="74017-454">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="74017-455">Aşağıdaki kod bir CORS varsayılan ilkesi ve `"AnotherPolicy"`adlı bir ilke oluşturur:</span><span class="sxs-lookup"><span data-stu-id="74017-455">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="74017-456">CORS'u devre dışı</span><span class="sxs-lookup"><span data-stu-id="74017-456">Disable CORS</span></span>

<span data-ttu-id="74017-457">[DisableCors&rbrack; denetleyici/sayfa modeli/eylem için CORS'u devre dışı bırakmaz. &lbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="74017-457">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="74017-458">CORS politika seçenekleri</span><span class="sxs-lookup"><span data-stu-id="74017-458">CORS policy options</span></span>

<span data-ttu-id="74017-459">Bu bölümde, BIR CORS ilkesinde ayarlanabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="74017-459">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="74017-460">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-460">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="74017-461">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-461">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="74017-462">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-462">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="74017-463">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-463">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="74017-464">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="74017-464">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="74017-465">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-465">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="74017-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>olarak `Startup.ConfigureServices`adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="74017-466"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="74017-467">Bazı seçenekler için, [önce CORS nasıl çalışır](#how-cors) bölümünü okumak yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-467">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="74017-468">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-468">Set the allowed origins</span></span>

<span data-ttu-id="74017-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Herhangi bir şema (veya)`http` `https`ile tüm kökenlerden CORS isteklerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-469"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="74017-470">`AllowAnyOrigin`herhangi bir *web sitesi* uygulamaya çapraz orijin isteklerinde bulunabileceğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-470">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="74017-471">Belirtilmesi `AllowAnyOrigin` `AllowCredentials` ve güvenli olmayan bir yapılandırmadır ve site ler arası istek sahteciliğiyle sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="74017-471">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="74017-472">Güvenli bir uygulama için, istemcinin sunucu kaynaklarına erişmek için kendisini yetkilendirmesi gerekiyorsa, tam bir kaynak listesi belirtin.</span><span class="sxs-lookup"><span data-stu-id="74017-472">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="74017-473">`AllowAnyOrigin`ön kontrol isteklerini `Access-Control-Allow-Origin` ve üstbilgietkiler.</span><span class="sxs-lookup"><span data-stu-id="74017-473">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="74017-474">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-474">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="74017-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; İlkenin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> özelliğini, kaynağın izin verilip verilmediğini değerlendirirken, kökenlerin yapılandırılmış bir joker etki alanıyla eşleşmesine olanak tanıyan bir işlev olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="74017-476">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-476">Set the allowed HTTP methods</span></span>

<span data-ttu-id="74017-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="74017-477"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="74017-478">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="74017-478">Allows any HTTP method:</span></span>
* <span data-ttu-id="74017-479">Ön kontrol isteklerini `Access-Control-Allow-Methods` ve üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="74017-479">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="74017-480">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-480">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="74017-481">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-481">Set the allowed request headers</span></span>

<span data-ttu-id="74017-482">Belirli üstbilginin CORS isteğinde gönderilmesine izin vermek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> yazar *istek üstbilgisini*arayın, arayın ve izin verilen üstbilgibelirtin:</span><span class="sxs-lookup"><span data-stu-id="74017-482">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="74017-483">Tüm yazar istek üstbilgilerine <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="74017-483">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="74017-484">Bu ayar, ön kontrol `Access-Control-Request-Headers` isteklerini ve üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="74017-484">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="74017-485">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-485">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="74017-486">CORS Middleware her zaman CorsPolicy.Headers yapılandırılan değerlerne bakılmaksızın gönderilmesi `Access-Control-Request-Headers` için dört üstbilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-486">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="74017-487">Bu üstbilgi listesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="74017-487">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="74017-488">Örneğin, aşağıdaki gibi yapılandırılan bir uygulamayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="74017-488">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="74017-489">CORS Middleware, her zaman beyaz listeye alındığı için ön `Content-Language` kontrol isteğine aşağıdaki istek üstbilgisiyle başarıyla yanıt verir:</span><span class="sxs-lookup"><span data-stu-id="74017-489">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="74017-490">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-490">Set the exposed response headers</span></span>

<span data-ttu-id="74017-491">Varsayılan olarak, tarayıcı tüm yanıt üstbilgisini uygulamaya maruz bırakmaz.</span><span class="sxs-lookup"><span data-stu-id="74017-491">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="74017-492">Daha fazla bilgi için [W3C Cross-Origin Kaynak Paylaşımı (Terminoloji): Basit Yanıt Üstbilgisi'ne](https://www.w3.org/TR/cors/#simple-response-header)bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-492">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="74017-493">Varsayılan olarak kullanılabilir yanıt üstbilgi:</span><span class="sxs-lookup"><span data-stu-id="74017-493">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="74017-494">CORS belirtimi bu üstbilgi *basit yanıt üstbilgi çağırır.*</span><span class="sxs-lookup"><span data-stu-id="74017-494">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="74017-495">Diğer üstbilgileri uygulamaiçin kullanılabilir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hale getirmek için aşağıdakileri arayın:</span><span class="sxs-lookup"><span data-stu-id="74017-495">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="74017-496">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="74017-496">Credentials in cross-origin requests</span></span>

<span data-ttu-id="74017-497">Kimlik bilgileri, CORS isteğinde özel kullanım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="74017-497">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="74017-498">Varsayılan olarak, tarayıcı bir çapraz kaynak isteği ile kimlik bilgileri göndermez.</span><span class="sxs-lookup"><span data-stu-id="74017-498">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="74017-499">Kimlik bilgileri çerezleri ve HTTP kimlik doğrulama şemalarını içerir.</span><span class="sxs-lookup"><span data-stu-id="74017-499">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="74017-500">Bir çapraz kaynak isteği ile kimlik bilgileri `XMLHttpRequest.withCredentials` göndermek `true`için, istemci nin .</span><span class="sxs-lookup"><span data-stu-id="74017-500">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="74017-501">Doğrudan `XMLHttpRequest` kullanma:</span><span class="sxs-lookup"><span data-stu-id="74017-501">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="74017-502">jQuery kullanma:</span><span class="sxs-lookup"><span data-stu-id="74017-502">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="74017-503">Getir [API'sini](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanma :</span><span class="sxs-lookup"><span data-stu-id="74017-503">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="74017-504">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="74017-504">The server must allow the credentials.</span></span> <span data-ttu-id="74017-505">Orijinler arası kimlik <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>bilgilerine izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="74017-505">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="74017-506">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgilerine izin verdiğini söyleyen bir üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="74017-506">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="74017-507">Tarayıcı kimlik bilgileri gönderir, ancak yanıt geçerli `Access-Control-Allow-Credentials` bir üstbilgi içermiyorsa, tarayıcı uygulamaya yanıtı açıklamaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="74017-507">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="74017-508">Orjinal kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="74017-508">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="74017-509">Başka bir etki alanında bulunan bir web sitesi, kullanıcının bilgisi olmadan kullanıcı adına oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-509">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="74017-510">CORS belirtimi, üstbilgi `"*"` varsa(tüm kökenlerin) kaynağını ayarlamanın `Access-Control-Allow-Credentials` geçersiz olduğunu da belirtir.</span><span class="sxs-lookup"><span data-stu-id="74017-510">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="74017-511">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="74017-511">Preflight requests</span></span>

<span data-ttu-id="74017-512">Bazı CORS istekleri için tarayıcı, gerçek isteği yapmadan önce ek bir istek gönderir.</span><span class="sxs-lookup"><span data-stu-id="74017-512">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="74017-513">Bu istek *bir ön kontrol isteği*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="74017-513">This request is called a *preflight request*.</span></span> <span data-ttu-id="74017-514">Aşağıdaki koşullar doğruysa, tarayıcı ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="74017-514">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="74017-515">İstek yöntemi GET, HEAD veya POST'dur.</span><span class="sxs-lookup"><span data-stu-id="74017-515">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="74017-516">Uygulama, istek `Accept`üstbilgisini , , `Accept-Language` `Content-Language`, `Content-Type`veya `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="74017-516">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="74017-517">Üstbilgi, `Content-Type` küme, aşağıdaki değerlerden birine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="74017-517">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="74017-518">İstemci isteği için ayarlanan istek üstbilgilerine ilişkin kural, `setRequestHeader` `XMLHttpRequest` uygulamanın nesneyi çağırarak ayarladığını üstbilgi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="74017-518">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="74017-519">CORS belirtimi bu üstbilgi *yazar istek üstbilgi*çağırır.</span><span class="sxs-lookup"><span data-stu-id="74017-519">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="74017-520">Kural, tarayıcının ayarlayabileceği `User-Agent`, , `Host`veya `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="74017-520">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="74017-521">Aşağıda, ön kontrol isteğine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="74017-521">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="74017-522">Uçuş öncesi istek HTTP OPTIONS yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="74017-522">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="74017-523">İki özel üstbilgi içerir:</span><span class="sxs-lookup"><span data-stu-id="74017-523">It includes two special headers:</span></span>

* <span data-ttu-id="74017-524">`Access-Control-Request-Method`: Gerçek istek için kullanılacak HTTP yöntemi.</span><span class="sxs-lookup"><span data-stu-id="74017-524">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="74017-525">`Access-Control-Request-Headers`: Uygulamanın gerçek istek üzerine ayarladığının istek üstbilgilerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="74017-525">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="74017-526">Daha önce belirtildiği gibi, `User-Agent`bu, tarayıcının belirlediği üstleri içermez.</span><span class="sxs-lookup"><span data-stu-id="74017-526">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="74017-527">CORS uygun ilke ile etkinleştirildiğinde, ASP.NET Core genellikle CORS uçuş öncesi isteklerine otomatik olarak yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="74017-527">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="74017-528">[Ön uçuş istekleri için [HttpOptions] özniteliğine](#pro)bakın.</span><span class="sxs-lookup"><span data-stu-id="74017-528">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="74017-529">CORS ön kontrol isteği, `Access-Control-Request-Headers` sunucuya gerçek istekle birlikte gönderilen üstbilgileri gösteren bir üstbilgi içerebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-529">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="74017-530">Belirli üstbilgi için: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="74017-530">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="74017-531">Tüm yazar istek üstbilgilerine <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="74017-531">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="74017-532">Tarayıcılar, nasıl ayarlandıkları `Access-Control-Request-Headers`konusunda tamamen tutarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-532">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="74017-533">Üstbilginizi (veya kullanım) `"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>dışında başka bir şeye `Accept`ayarlarsanız, en az , `Content-Type`ve `Origin`, artı desteklemek istediğiniz özel üstbilgi içermelidir.</span><span class="sxs-lookup"><span data-stu-id="74017-533">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="74017-534">Aşağıda, ön kontrol isteğine örnek bir yanıt verilmiştir (sunucunun isteğe izin verdiğini varsayarsak):</span><span class="sxs-lookup"><span data-stu-id="74017-534">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="74017-535">Yanıt, izin `Access-Control-Allow-Methods` verilen yöntemleri ve isteğe bağlı `Access-Control-Allow-Headers` olarak izin verilen üstbilgilisteleyen bir üstbilgi içeren bir üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="74017-535">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="74017-536">Ön kontrol isteği başarılı olursa, tarayıcı gerçek isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="74017-536">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="74017-537">Ön kontrol isteği reddedilirse, uygulama *200 Tamam* yanıtını döndürür, ancak CORS üstbilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="74017-537">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="74017-538">Bu nedenle, tarayıcı çapraz kaynak isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="74017-538">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="74017-539">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="74017-539">Set the preflight expiration time</span></span>

<span data-ttu-id="74017-540">Üstbilgi, `Access-Control-Max-Age` ön kontrol isteğine verilen yanıtın ne kadar süreyle önbelleğe alınabileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="74017-540">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="74017-541">Bu üstbilgi ayarlamak <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>için:</span><span class="sxs-lookup"><span data-stu-id="74017-541">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="74017-542">CORS nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="74017-542">How CORS works</span></span>

<span data-ttu-id="74017-543">Bu bölümde, HTTP iletileri düzeyinde bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğine neler olduğu açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="74017-543">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="74017-544">CORS bir güvenlik özelliği **değildir.**</span><span class="sxs-lookup"><span data-stu-id="74017-544">CORS is **not** a security feature.</span></span> <span data-ttu-id="74017-545">CORS, bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-545">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="74017-546">Örneğin, kötü niyetli bir aktör sitenize karşı [Site Ötesi Komut Dosyasını (XSS) engelleyebilir](xref:security/cross-site-scripting) ve CORS özellikli sitesine bilgi çalmak için bir çapraz site isteği gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-546">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="74017-547">CORS'e izin vererek API'niz daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="74017-547">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="74017-548">CORS'i uygulamak istemciye (tarayıcıya) kalmış.</span><span class="sxs-lookup"><span data-stu-id="74017-548">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="74017-549">Sunucu isteği yürütür ve yanıtı döndürür, bir hata döndürür ve yanıtı engeller istemcisidir.</span><span class="sxs-lookup"><span data-stu-id="74017-549">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="74017-550">Örneğin, aşağıdaki araçlardan herhangi biri sunucu yanıtını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="74017-550">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="74017-551">Fiddler</span><span class="sxs-lookup"><span data-stu-id="74017-551">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="74017-552">Postman</span><span class="sxs-lookup"><span data-stu-id="74017-552">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="74017-553">.NET Https://yayla</span><span class="sxs-lookup"><span data-stu-id="74017-553">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="74017-554">Adres çubuğuna URL girerek bir web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="74017-554">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="74017-555">Bu, bir sunucunun tarayıcıların aksi takdirde yasaklanacak bir çapraz kökenli [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteğini yürütmesine izin vermenin bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="74017-555">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="74017-556">Tarayıcılar (CORS olmadan) çapraz kökenli isteklerde olamaz.</span><span class="sxs-lookup"><span data-stu-id="74017-556">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="74017-557">CORS önce, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) bu kısıtlamayı atlatmak için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="74017-557">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="74017-558">JSONP XHR kullanmaz, yanıtı `<script>` almak için etiketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="74017-558">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="74017-559">Komut dosyalarıçapraz kökenli yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-559">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="74017-560">[CORS belirtimi,](https://www.w3.org/TR/cors/) orijinler arası istekleri etkinleştiren birkaç yeni HTTP üstbilgisini tanıttı.</span><span class="sxs-lookup"><span data-stu-id="74017-560">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="74017-561">Bir tarayıcı CORS'i destekliyorsa, bu başlıkları çapraz başlangıç istekleri için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-561">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="74017-562">CORS'i etkinleştirmek için özel JavaScript kodu gerekmez.</span><span class="sxs-lookup"><span data-stu-id="74017-562">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="74017-563">Aşağıda, bir çapraz kaynak isteği örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="74017-563">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="74017-564">Üstbilgi, `Origin` isteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="74017-564">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="74017-565">Üstbilgi `Origin` gereklidir ve ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-565">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="74017-566">Sunucu isteğe izin veriyorsa, yanıttaki üstbilgiyi `Access-Control-Allow-Origin` ayarlar.</span><span class="sxs-lookup"><span data-stu-id="74017-566">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="74017-567">Bu üstbilginin değeri, `Origin` istekteki üstbilgiyle eşleşir veya `"*"`joker karakter değeridir, yani herhangi bir menşee izin verilir:</span><span class="sxs-lookup"><span data-stu-id="74017-567">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="74017-568">Yanıt `Access-Control-Allow-Origin` üstbilgi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="74017-568">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="74017-569">Özellikle, tarayıcı isteği izin verir.</span><span class="sxs-lookup"><span data-stu-id="74017-569">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="74017-570">Sunucu başarılı bir yanıt döndürse bile, tarayıcı yanıtı istemci uygulamasına sunmaz.</span><span class="sxs-lookup"><span data-stu-id="74017-570">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="74017-571">Test CORS</span><span class="sxs-lookup"><span data-stu-id="74017-571">Test CORS</span></span>

<span data-ttu-id="74017-572">CORS'u test etmek için:</span><span class="sxs-lookup"><span data-stu-id="74017-572">To test CORS:</span></span>

1. <span data-ttu-id="74017-573">[Bir API projesi oluşturun.](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="74017-573">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="74017-574">Alternatif olarak, [örnek indirebilirsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)</span><span class="sxs-lookup"><span data-stu-id="74017-574">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="74017-575">Bu belgedeki yaklaşımlardan birini kullanarak CORS'i etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="74017-575">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="74017-576">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="74017-576">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="74017-577">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)benzer bir örnek uygulamayı test etmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-577">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="74017-578">Bir web uygulaması projesi (Razor Pages veya MVC) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="74017-578">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="74017-579">Örnekte Razor Pages kullanır.</span><span class="sxs-lookup"><span data-stu-id="74017-579">The sample uses Razor Pages.</span></span> <span data-ttu-id="74017-580">Web uygulamasını API projesiyle aynı çözümde oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="74017-580">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="74017-581">*Index.cshtml* dosyasına aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="74017-581">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="74017-582">Önceki kodda, dağıtılan `url: 'https://<web app>.azurewebsites.net/api/values/1',` uygulamanın URL'si ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="74017-582">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="74017-583">API projesini dağıtın.</span><span class="sxs-lookup"><span data-stu-id="74017-583">Deploy the API project.</span></span> <span data-ttu-id="74017-584">Örneğin, [Azure'a dağıtın.](xref:host-and-deploy/azure-apps/index)</span><span class="sxs-lookup"><span data-stu-id="74017-584">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="74017-585">Masaüstünden Razor Pages veya MVC uygulamasını çalıştırın ve **Test** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="74017-585">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="74017-586">Hata iletilerini gözden geçirmek için F12 araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="74017-586">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="74017-587">Yerel ana bilgisayar `WithOrigins` kaynağını kaldırın ve uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="74017-587">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="74017-588">Alternatif olarak, istemci uygulamasını farklı bir bağlantı noktasıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="74017-588">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="74017-589">Örneğin, Visual Studio çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="74017-589">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="74017-590">İstemci uygulaması yla test edin.</span><span class="sxs-lookup"><span data-stu-id="74017-590">Test with the client app.</span></span> <span data-ttu-id="74017-591">CORS hataları bir hata döndürer, ancak hata iletisi JavaScript için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="74017-591">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="74017-592">Hatayı görmek için F12 araçlarındaki konsol sekmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="74017-592">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="74017-593">Tarayıcıya bağlı olarak, aşağıdakilere benzer bir hata (F12 araçları konsolunda) alırsınız:</span><span class="sxs-lookup"><span data-stu-id="74017-593">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="74017-594">Microsoft Edge'i kullanma:</span><span class="sxs-lookup"><span data-stu-id="74017-594">Using Microsoft Edge:</span></span>

     <span data-ttu-id="74017-595">**SEC7120: [CORS] `https://localhost:44375` Menşei, erişim kaynağı kaynağı için Access-Control-Allow-Origin yanıt `https://localhost:44375` başlığında`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="74017-595">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="74017-596">Chrome'u kullanma:</span><span class="sxs-lookup"><span data-stu-id="74017-596">Using Chrome:</span></span>

     <span data-ttu-id="74017-597">**XMLHttpRequest'e `https://webapi.azurewebsites.net/api/values/1` orijinli `https://localhost:44375` erişim CORS ilkesi tarafından engellendi: İstenen kaynakta 'Access-Control-Allow-Origin' başlığı bulunmamaktadır.**</span><span class="sxs-lookup"><span data-stu-id="74017-597">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="74017-598">CORS özellikli uç [noktalar, Kemancı](https://www.telerik.com/fiddler) veya [Postacı](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="74017-598">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="74017-599">Bir araç kullanırken, `Origin` üstbilgi tarafından belirtilen isteğin kaynağı, isteği alan ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="74017-599">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="74017-600">Üstbilginin değerine *cross-origin* `Origin` göre istek çapraz kökenli değilse:</span><span class="sxs-lookup"><span data-stu-id="74017-600">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="74017-601">CORS Middleware'in isteği işlemesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="74017-601">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="74017-602">CORS başlıkları yanıt olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="74017-602">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="74017-603">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="74017-603">CORS in IIS</span></span>

<span data-ttu-id="74017-604">IIS'ye dağıtılırken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS'in Windows Kimlik Doğrulama'dan önce çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="74017-604">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="74017-605">Bu senaryoyu desteklemek için [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmesi ve yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="74017-605">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="74017-606">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="74017-606">Additional resources</span></span>

* [<span data-ttu-id="74017-607">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="74017-607">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="74017-608">IIS CORS modülü ile başlarken</span><span class="sxs-lookup"><span data-stu-id="74017-608">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
