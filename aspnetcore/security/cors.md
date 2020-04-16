---
title: ASP.NET Çekirdekte Orijinler Arası İstekleri (CORS) etkinleştirme
author: rick-anderson
description: ASP.NET Core uygulamasında, KORS'un çapraz orijin isteklerine izin vermek veya reddetmek için nasıl bir standart olduğunu öğrenin.
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: e7731fd967c206679ac93209fdb84f40367bea37
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440915"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="0506d-103">ASP.NET Çekirdekte Orijinler Arası İstekleri (CORS) etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="0506d-103">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0506d-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk Larkin](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="0506d-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="0506d-105">Bu makalede, bir ASP.NET Core uygulamasında CORS'in nasıl etkinleştirilen gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0506d-105">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="0506d-106">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="0506d-106">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="0506d-107">Bu kısıtlamaya *aynı kaynak ilkesi*denir.</span><span class="sxs-lookup"><span data-stu-id="0506d-107">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="0506d-108">Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="0506d-108">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="0506d-109">Bazen, diğer sitelerin uygulamanızda çapraz orijin isteklerinde yer almasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0506d-109">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="0506d-110">Daha fazla bilgi için [Mozilla CORS makalesine](https://developer.mozilla.org/docs/Web/HTTP/CORS)bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-110">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="0506d-111">[Çapraz Kaynak Paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="0506d-111">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="0506d-112">Bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-112">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="0506d-113">Bir güvenlik özelliği **değildir,** CORS güvenliği rahatlatır.</span><span class="sxs-lookup"><span data-stu-id="0506d-113">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="0506d-114">BIR API CORS izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-114">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="0506d-115">Daha fazla bilgi için [CORS'in nasıl çalıştığını](#how-cors)görün.</span><span class="sxs-lookup"><span data-stu-id="0506d-115">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="0506d-116">Sunucunun, diğerlerini reddederken bazı kişiler arası isteklere açıkça izin vermesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-116">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="0506d-117">[JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerden daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="0506d-117">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="0506d-118">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0506d-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="0506d-119">Aynı köken</span><span class="sxs-lookup"><span data-stu-id="0506d-119">Same origin</span></span>

<span data-ttu-id="0506d-120">Aynı düzenleri, ana bilgisayarlar ve bağlantı noktaları[(RFC 6454)](https://tools.ietf.org/html/rfc6454)varsa, iki URL aynı kökene sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0506d-120">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="0506d-121">Bu iki URL aynı kökene sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0506d-121">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="0506d-122">Bu URL'lerin önceki iki URL'den farklı kökenleri vardır:</span><span class="sxs-lookup"><span data-stu-id="0506d-122">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="0506d-123">`https://example.net`&ndash; Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="0506d-123">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="0506d-124">`https://www.example.com/foo.html`&ndash; Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="0506d-124">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="0506d-125">`http://example.com/foo.html`&ndash; Farklı şema</span><span class="sxs-lookup"><span data-stu-id="0506d-125">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="0506d-126">`https://example.com:9000/foo.html`&ndash; Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="0506d-126">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="0506d-127">CORS'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="0506d-127">Enable CORS</span></span>

<span data-ttu-id="0506d-128">CORS'i etkinleştirmenin üç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="0506d-128">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="0506d-129">Adlandırılmış bir [ilke](#np) veya [varsayılan ilke](#dp)kullanarak ara yazılımda.</span><span class="sxs-lookup"><span data-stu-id="0506d-129">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="0506d-130">[Uç nokta yönlendirmeyi](#ecors)kullanma.</span><span class="sxs-lookup"><span data-stu-id="0506d-130">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="0506d-131">[[EnableCors]](#attr) özelliği ile.</span><span class="sxs-lookup"><span data-stu-id="0506d-131">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="0506d-132">[[EnableCors]](#attr) özniteliğinin adlandırılmış bir ilkeyle kullanılması, CORS'i destekleyen uç noktaları sınırlamada en iyi denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-132">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="0506d-133">Her yaklaşım aşağıdaki bölümlerde ayrıntılı olarak açıklanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-133">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="0506d-134">Adlı ilke ve middleware ile CORS</span><span class="sxs-lookup"><span data-stu-id="0506d-134">CORS with named policy and middleware</span></span>

<span data-ttu-id="0506d-135">CORS Middleware, çapraz orijin isteklerini işler.</span><span class="sxs-lookup"><span data-stu-id="0506d-135">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="0506d-136">Aşağıdaki kod, belirtilen kökenlere sahip uygulamanın tüm uç noktalarına bir CORS ilkesi uygular:</span><span class="sxs-lookup"><span data-stu-id="0506d-136">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="0506d-137">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0506d-137">The preceding code:</span></span>

* <span data-ttu-id="0506d-138">İlke adını `_myAllowSpecificOrigins`' a ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-138">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="0506d-139">İlke adı rasgele.</span><span class="sxs-lookup"><span data-stu-id="0506d-139">The policy name is arbitrary.</span></span>
* <span data-ttu-id="0506d-140">Uzantı <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> yöntemini çağırır ve `_myAllowSpecificOrigins` CORS ilkesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0506d-140">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="0506d-141">`UseCors`CORS ara yazılımekler.</span><span class="sxs-lookup"><span data-stu-id="0506d-141">`UseCors` adds the CORS middleware.</span></span>
* <span data-ttu-id="0506d-142"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile aramalar .</span><span class="sxs-lookup"><span data-stu-id="0506d-142">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0506d-143">Lambda bir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nesne alır.</span><span class="sxs-lookup"><span data-stu-id="0506d-143">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="0506d-144">[Yapılandırma seçenekleri](#cors-policy-options), `WithOrigins`örneğin, bu makalede daha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0506d-144">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="0506d-145">Tüm denetleyici `_myAllowSpecificOrigins` uç noktaları için CORS ilkesini etkinleştiri.</span><span class="sxs-lookup"><span data-stu-id="0506d-145">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="0506d-146">Belirli uç noktalara BIR CORS ilkesi uygulamak için [uç nokta yönlendirmesine](#ecors) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-146">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="0506d-147">Uç nokta yönlendirmesi ile, CORS ara ware ve aramalar `UseRouting` arasında `UseEndpoints`yürütmek için ***yapılandırılmalıdır.***</span><span class="sxs-lookup"><span data-stu-id="0506d-147">With endpoint routing, the CORS middleware ***must*** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="0506d-148">Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#testc) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-148">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="0506d-149">Yöntem <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> çağrısı, uygulamanın servis konteynerine CORS hizmetlerini ekler:</span><span class="sxs-lookup"><span data-stu-id="0506d-149">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="0506d-150">Daha fazla bilgi için bu belgedeki [CORS ilkesi seçeneklerine](#cpo) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-150">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="0506d-151">Yöntemler, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> aşağıdaki kodda gösterildiği gibi zincirlenebilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-151">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="0506d-152">Not: Belirtilen URL, sondaki eğik`/`çizgi () **içermemelidir.**</span><span class="sxs-lookup"><span data-stu-id="0506d-152">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="0506d-153">URL ile sonlandırma, `/`karşılaştırma `false` döndürür ve hiçbir üstbilgi döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0506d-153">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="0506d-154">Varsayılan ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="0506d-154">CORS with default policy and middleware</span></span>

<span data-ttu-id="0506d-155">Aşağıdaki vurgulanan kod varsayılan CORS ilkesini sağlar:</span><span class="sxs-lookup"><span data-stu-id="0506d-155">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="0506d-156">Önceki kod, varsayılan CORS ilkesini tüm denetleyici uç noktalarına uygular.</span><span class="sxs-lookup"><span data-stu-id="0506d-156">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="0506d-157">Uç nokta yönlendirme ile Kors'u etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="0506d-157">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="0506d-158">Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş [isteklerini](#apf) ***desteklemez.***</span><span class="sxs-lookup"><span data-stu-id="0506d-158">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="0506d-159">Daha fazla bilgi [için, bu GitHub sorunu](https://github.com/dotnet/aspnetcore/issues/20709) ve [test CORS uç noktası yönlendirme ve [HttpOptions] bakın.](#tcer)</span><span class="sxs-lookup"><span data-stu-id="0506d-159">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="0506d-160">Uç nokta yönlendirmeile CORS, uzantı yöntemleri <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> kümesi kullanılarak uç nokta başına etkinleştirilebilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-160">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="0506d-161">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="0506d-161">In the preceding code:</span></span>

* <span data-ttu-id="0506d-162">`app.UseCors`CORS ara yazılımını sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-162">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="0506d-163">Varsayılan ilke yapılandırılmadığından, `app.UseCors()` tek başına CORS'u etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="0506d-163">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="0506d-164">Ve `/echo` denetleyici uç noktaları belirtilen ilkeyi kullanarak orijinler arası isteklere izin verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-164">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="0506d-165">Ve `/echo2` Razor Pages bitiş noktaları, varsayılan ilke belirtilmedi, çünkü çapraz başlangıç isteklerine izin ***vermez.***</span><span class="sxs-lookup"><span data-stu-id="0506d-165">The `/echo2` and Razor Pages endpoints do ***not*** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="0506d-166">[[Engelleyici Korse]](#dc) özelliği, uç nokta yönlendirme ile `RequireCors`etkinleştirilen CORS'leri ***devre*** dışı almaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-166">The [[DisableCors]](#dc) attribute does ***not***  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="0506d-167">Yukarıdakine benzer test koduyla ilgili talimatlar için [uç nokta yönlendirme stoyonu ve [HttpOptions] içeren Test CORS'e](#tcer) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-167">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="0506d-168">Özniteliklerle CORS'i etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="0506d-168">Enable CORS with attributes</span></span>

<span data-ttu-id="0506d-169">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özelliği ile CORS etkinleştirme ve yalnızca CORS gerektiren uç noktalara adlandırılmış bir ilke uygulamak en iyi denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-169">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="0506d-170">[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliği, CORS'u küresel olarak uygulamak için bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-170">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="0506d-171">Öznitelik, `[EnableCors]` tüm uç noktalar yerine seçili uç noktalar için CORS'e olanak sağlar:</span><span class="sxs-lookup"><span data-stu-id="0506d-171">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="0506d-172">`[EnableCors]`varsayılan ilkeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="0506d-172">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="0506d-173">`[EnableCors("{Policy String}")]`adlı bir ilke belirtir.</span><span class="sxs-lookup"><span data-stu-id="0506d-173">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="0506d-174">Öznitelik `[EnableCors]` uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-174">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="0506d-175">Jilet Sayfası`PageModel`</span><span class="sxs-lookup"><span data-stu-id="0506d-175">Razor Page `PageModel`</span></span>
* <span data-ttu-id="0506d-176">Denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="0506d-176">Controller</span></span>
* <span data-ttu-id="0506d-177">Denetleyici eylem yöntemi</span><span class="sxs-lookup"><span data-stu-id="0506d-177">Controller action method</span></span>

<span data-ttu-id="0506d-178">Öznitelik ile denetleyicilere, sayfa modellerine veya eylem `[EnableCors]` yöntemlerine farklı ilkeler uygulanabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-178">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="0506d-179">Öznitelik `[EnableCors]` bir denetleyici, sayfa modeli veya eylem yöntemine uygulandığında ve ARA Yazılım'da CORS etkinleştirildiğinde, ***her iki*** ilke de uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-179">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="0506d-180">\*\*\*Politikaların birleştirilmesine karşı tavsiye ediyoruz. \*\*\*Her ***ikisini de aynı uygulamada değil, öznitelik veya ara yazılımı kullanın.*** `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="0506d-180">***We recommend against combining policies. Use the*** `[EnableCors]` ***attribute or middleware, not both in the same app.***</span></span>

<span data-ttu-id="0506d-181">Aşağıdaki kod her yöntem için farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="0506d-181">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="0506d-182">Aşağıdaki kod iki CORS ilkeleri oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0506d-182">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="0506d-183">CORS isteklerini sınırlandırma nın en iyi kontrolü için:</span><span class="sxs-lookup"><span data-stu-id="0506d-183">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="0506d-184">Adlandırılmış bir ilke ile kullanın. `[EnableCors("MyPolicy")]`</span><span class="sxs-lookup"><span data-stu-id="0506d-184">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="0506d-185">Varsayılan bir ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="0506d-185">Don't define a default policy.</span></span>
* <span data-ttu-id="0506d-186">Uç nokta [yönlendirmekullanmayın.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="0506d-186">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="0506d-187">Sonraki bölümdeki kod önceki listeyi karşılar.</span><span class="sxs-lookup"><span data-stu-id="0506d-187">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="0506d-188">Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#testc) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-188">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="0506d-189">CORS'u devre dışı</span><span class="sxs-lookup"><span data-stu-id="0506d-189">Disable CORS</span></span>

<span data-ttu-id="0506d-190">[[Engelleyici Ler]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) [özniteliği, bitiş noktası yönlendirmesi](#ecors)ile etkinleştirilen CORS'leri devre ***dışı*** almaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-190">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does ***not***  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="0506d-191">Aşağıdaki kod CORS ilkesini `"MyPolicy"`tanımlar:</span><span class="sxs-lookup"><span data-stu-id="0506d-191">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="0506d-192">Aşağıdaki kod `GetValues2` eylem için CORS devre dışı kalmaktadır:</span><span class="sxs-lookup"><span data-stu-id="0506d-192">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="0506d-193">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0506d-193">The preceding code:</span></span>

* <span data-ttu-id="0506d-194">[Uç nokta yönlendirmeile](#ecors)CORS'u etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="0506d-194">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="0506d-195">[Varsayılan BIR CORS ilkesi](#dp)tanımlamaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-195">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="0506d-196">Denetleyiciiçin CORS ilkesini `"MyPolicy"` etkinleştirmek için [[EnableCors("MyPolicy")]](#attr) kullanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-196">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="0506d-197">`GetValues2` Yöntem için CORS'u devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-197">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="0506d-198">Yukarıdaki kodun sınanma talimatları için [Test CORS'a](#testc) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-198">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="0506d-199">CORS politika seçenekleri</span><span class="sxs-lookup"><span data-stu-id="0506d-199">CORS policy options</span></span>

<span data-ttu-id="0506d-200">Bu bölümde, BIR CORS ilkesinde ayarlanabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="0506d-200">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="0506d-201">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-201">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="0506d-202">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-202">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="0506d-203">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-203">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="0506d-204">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-204">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="0506d-205">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="0506d-205">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="0506d-206">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-206">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="0506d-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>olarak `Startup.ConfigureServices`adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0506d-207"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0506d-208">Bazı seçenekler için, [önce CORS nasıl çalışır](#how-cors) bölümünü okumak yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-208">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="0506d-209">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-209">Set the allowed origins</span></span>

<span data-ttu-id="0506d-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Herhangi bir şema (veya)`http` `https`ile tüm kökenlerden CORS isteklerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-210"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="0506d-211">`AllowAnyOrigin`herhangi bir *web sitesi* uygulamaya çapraz orijin isteklerinde bulunabileceğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-211">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0506d-212">Belirtilmesi `AllowAnyOrigin` `AllowCredentials` ve güvenli olmayan bir yapılandırmadır ve site ler arası istek sahteciliğiyle sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-212">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="0506d-213">Bir uygulama her iki yöntemle de yapılandırıldığında CORS hizmeti geçersiz bir CORS yanıtını döndürür.</span><span class="sxs-lookup"><span data-stu-id="0506d-213">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="0506d-214">`AllowAnyOrigin`ön kontrol isteklerini `Access-Control-Allow-Origin` ve üstbilgietkiler.</span><span class="sxs-lookup"><span data-stu-id="0506d-214">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="0506d-215">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-215">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0506d-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; İlkenin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> özelliğini, kaynağın izin verilip verilmediğini değerlendirirken, kökenlerin yapılandırılmış bir joker etki alanıyla eşleşmesine olanak tanıyan bir işlev olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="0506d-217">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-217">Set the allowed HTTP methods</span></span>

<span data-ttu-id="0506d-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="0506d-218"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="0506d-219">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="0506d-219">Allows any HTTP method:</span></span>
* <span data-ttu-id="0506d-220">Ön kontrol isteklerini `Access-Control-Allow-Methods` ve üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="0506d-220">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="0506d-221">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="0506d-222">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-222">Set the allowed request headers</span></span>

<span data-ttu-id="0506d-223">Belirli üstbilginin CORS isteğinde gönderilmesine izin vermek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> yazar [istek üstbilgisini](https://xhr.spec.whatwg.org/#request)arayın, arayın ve izin verilen üstbilgibelirtin:</span><span class="sxs-lookup"><span data-stu-id="0506d-223">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="0506d-224">Tüm [yazar istek üstbilgilerine](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için , arayın: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="0506d-224">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="0506d-225">`AllowAnyHeader`ön kontrol isteklerini ve [Erişim-Kontrol-İstek-Üstbilgi](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) üstbilgisini etkiler.</span><span class="sxs-lookup"><span data-stu-id="0506d-225">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="0506d-226">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-226">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0506d-227">Cors Middleware ilkesi tarafından `WithHeaders` belirtilen belirli üstbilgilerle eşleşerek, `Access-Control-Request-Headers` yalnızca gönderilen üstbilgiler `WithHeaders`üstbilgilerle tam olarak eşleştiğinde mümkündür.</span><span class="sxs-lookup"><span data-stu-id="0506d-227">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="0506d-228">Örneğin, aşağıdaki gibi yapılandırılan bir uygulamayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="0506d-228">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="0506d-229">([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) listede yer olmadığı `Content-Language` için CORS Middleware aşağıdaki istek üstbilgisi ile ön kontrol isteğini `WithHeaders`reddeder:</span><span class="sxs-lookup"><span data-stu-id="0506d-229">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="0506d-230">Uygulama *200 Ok* yanıtı döndürür, ancak CORS üstbilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="0506d-230">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="0506d-231">Bu nedenle, tarayıcı çapraz kaynak isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="0506d-231">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="0506d-232">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-232">Set the exposed response headers</span></span>

<span data-ttu-id="0506d-233">Varsayılan olarak, tarayıcı tüm yanıt üstbilgisini uygulamaya maruz bırakmaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-233">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="0506d-234">Daha fazla bilgi için [W3C Cross-Origin Kaynak Paylaşımı (Terminoloji): Basit Yanıt Üstbilgisi'ne](https://www.w3.org/TR/cors/#simple-response-header)bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-234">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="0506d-235">Varsayılan olarak kullanılabilir yanıt üstbilgi:</span><span class="sxs-lookup"><span data-stu-id="0506d-235">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="0506d-236">CORS belirtimi bu üstbilgi *basit yanıt üstbilgi çağırır.*</span><span class="sxs-lookup"><span data-stu-id="0506d-236">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="0506d-237">Diğer üstbilgileri uygulamaiçin kullanılabilir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hale getirmek için aşağıdakileri arayın:</span><span class="sxs-lookup"><span data-stu-id="0506d-237">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="0506d-238">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="0506d-238">Credentials in cross-origin requests</span></span>

<span data-ttu-id="0506d-239">Kimlik bilgileri, CORS isteğinde özel kullanım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0506d-239">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="0506d-240">Varsayılan olarak, tarayıcı bir çapraz kaynak isteği ile kimlik bilgileri göndermez.</span><span class="sxs-lookup"><span data-stu-id="0506d-240">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="0506d-241">Kimlik bilgileri çerezleri ve HTTP kimlik doğrulama şemalarını içerir.</span><span class="sxs-lookup"><span data-stu-id="0506d-241">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="0506d-242">Bir çapraz kaynak isteği ile kimlik bilgileri `XMLHttpRequest.withCredentials` göndermek `true`için, istemci nin .</span><span class="sxs-lookup"><span data-stu-id="0506d-242">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="0506d-243">Doğrudan `XMLHttpRequest` kullanma:</span><span class="sxs-lookup"><span data-stu-id="0506d-243">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="0506d-244">jQuery kullanma:</span><span class="sxs-lookup"><span data-stu-id="0506d-244">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="0506d-245">Getir [API'sini](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanma :</span><span class="sxs-lookup"><span data-stu-id="0506d-245">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="0506d-246">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-246">The server must allow the credentials.</span></span> <span data-ttu-id="0506d-247">Orijinler arası kimlik <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>bilgilerine izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="0506d-247">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="0506d-248">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgilerine izin verdiğini söyleyen bir üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="0506d-248">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="0506d-249">Tarayıcı kimlik bilgileri gönderir, ancak yanıt geçerli `Access-Control-Allow-Credentials` bir üstbilgi içermiyorsa, tarayıcı uygulamaya yanıtı açıklamaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="0506d-249">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="0506d-250">Orjinal kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-250">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="0506d-251">Başka bir etki alanında bulunan bir web sitesi, kullanıcının bilgisi olmadan kullanıcı adına oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-251">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="0506d-252">CORS belirtimi, üstbilgi `"*"` varsa(tüm kökenlerin) kaynağını ayarlamanın `Access-Control-Allow-Credentials` geçersiz olduğunu da belirtir.</span><span class="sxs-lookup"><span data-stu-id="0506d-252">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="0506d-253">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="0506d-253">Preflight requests</span></span>

<span data-ttu-id="0506d-254">Bazı CORS istekleri için tarayıcı, gerçek isteği yapmadan önce ek bir [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="0506d-254">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="0506d-255">Bu istek [bir ön kontrol isteği](https://developer.mozilla.org/docs/Glossary/Preflight_request)olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0506d-255">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="0506d-256">Aşağıdaki ***tüm*** koşullar doğruysa, tarayıcı ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-256">The browser can skip the preflight request if ***all*** the following conditions are true:</span></span>

* <span data-ttu-id="0506d-257">İstek yöntemi GET, HEAD veya POST'dur.</span><span class="sxs-lookup"><span data-stu-id="0506d-257">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="0506d-258">Uygulama, istek `Accept`üstbilgisini , , `Accept-Language` `Content-Language`, `Content-Type`veya `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="0506d-258">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="0506d-259">Üstbilgi, `Content-Type` küme, aşağıdaki değerlerden birine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0506d-259">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="0506d-260">İstemci isteği için ayarlanan istek üstbilgilerine ilişkin kural, `setRequestHeader` `XMLHttpRequest` uygulamanın nesneyi çağırarak ayarladığını üstbilgi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-260">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="0506d-261">CORS belirtimi bu üstbilgi [yazar istek üstbilgi](https://www.w3.org/TR/cors/#author-request-headers)çağırır.</span><span class="sxs-lookup"><span data-stu-id="0506d-261">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="0506d-262">Kural, tarayıcının ayarlayabileceği `User-Agent`, , `Host`veya `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="0506d-262">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="0506d-263">Aşağıda, bu belgenin [Test CORS](#testc) bölümündeki **[Test Yap]** düğmesinden yapılan ön kontrol isteğine benzer bir örnek yanıt verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0506d-263">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="0506d-264">Ön kontrol isteği [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-264">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="0506d-265">Aşağıdaki üstbilgi içerebilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-265">It may include the following headers:</span></span>

* <span data-ttu-id="0506d-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): Gerçek istek için kullanılacak HTTP yöntemi.</span><span class="sxs-lookup"><span data-stu-id="0506d-266">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="0506d-267">[Erişim-Denetim-İstek-Üstbilgi](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): Uygulamanın gerçek istek üzerine ayarladığının istek üstbilgilerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="0506d-267">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="0506d-268">Daha önce belirtildiği gibi, `User-Agent`bu, tarayıcının belirlediği üstleri içermez.</span><span class="sxs-lookup"><span data-stu-id="0506d-268">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="0506d-269">Erişim-Denetim-İzin Verme-Yöntemler</span><span class="sxs-lookup"><span data-stu-id="0506d-269">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="0506d-270">Ön kontrol isteği reddedilirse, uygulama bir `200 OK` yanıt verir, ancak CORS üstbilgisini ayarlamaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-270">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="0506d-271">Bu nedenle, tarayıcı çapraz kaynak isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="0506d-271">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="0506d-272">Reddedilen ön kontrol isteği örneği için, bu belgenin [Test CORS](#testc) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-272">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="0506d-273">Konsol uygulaması, F12 araçlarını kullanarak tarayıcıya bağlı olarak aşağıdakilerden birine benzer bir hata gösterir:</span><span class="sxs-lookup"><span data-stu-id="0506d-273">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="0506d-274">Firefox: Cross-Origin İstek Engellendi: Aynı Başlangıç Politikası uzak `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`kaynağı okuma ya da izin vermiyor.</span><span class="sxs-lookup"><span data-stu-id="0506d-274">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="0506d-275">(Sebep: CORS isteği başarılı olmadı).</span><span class="sxs-lookup"><span data-stu-id="0506d-275">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="0506d-276">Daha Fazla Bilgi Edinin</span><span class="sxs-lookup"><span data-stu-id="0506d-276">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="0506d-277">Krom bazlı: ' 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5from origin 'https://cors3.azurewebsites.net' 'eve erişim CORS ilkesi tarafından engellendi: Ön kontrol isteğine yanıt erişim denetiminden geçmiyor: İstenen kaynakta 'Access-Control-Allow-Allow-Origin' başlığı bulunmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="0506d-277">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="0506d-278">İhtiyaçlarınıza opak bir yanıt yanıt veriyorsa, CORS devre dışı bırakılmış kaynağı almak için istek modunu 'kors'a göre ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0506d-278">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="0506d-279">Belirli üstbilgi için: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="0506d-279">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="0506d-280">Tüm [yazar istek üstbilgilerine](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için , arayın: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*></span><span class="sxs-lookup"><span data-stu-id="0506d-280">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="0506d-281">Tarayıcılar, ayarlarken `Access-Control-Request-Headers`tutarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-281">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="0506d-282">Eğer ikisinden biri:</span><span class="sxs-lookup"><span data-stu-id="0506d-282">If either:</span></span>

* <span data-ttu-id="0506d-283">Üstbilgi, başka bir şeye ayarlanır`"*"`</span><span class="sxs-lookup"><span data-stu-id="0506d-283">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="0506d-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>denir: En az `Accept` `Content-Type`, `Origin`, ve , artı desteklemek istediğiniz herhangi bir özel üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="0506d-284"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="0506d-285">Otomatik ön kontrol istek kodu</span><span class="sxs-lookup"><span data-stu-id="0506d-285">Automatic preflight request code</span></span>

<span data-ttu-id="0506d-286">CORS ilkesi uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="0506d-286">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="0506d-287">Global olarak `app.UseCors` arayarak `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0506d-287">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="0506d-288">Özniteliği `[EnableCors]` kullanarak.</span><span class="sxs-lookup"><span data-stu-id="0506d-288">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="0506d-289">ASP.NET Core, uçuş öncesi OPTIONS isteğine yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-289">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="0506d-290">Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş isteklerini ***desteklemez.***</span><span class="sxs-lookup"><span data-stu-id="0506d-290">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support automatic preflight requests.</span></span>

<span data-ttu-id="0506d-291">Bu belgenin [Test CORS](#testc) bölümü bu davranışı gösterir.</span><span class="sxs-lookup"><span data-stu-id="0506d-291">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="0506d-292">[HttpOptions] ön kontrol istekleri için öznitelik</span><span class="sxs-lookup"><span data-stu-id="0506d-292">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="0506d-293">CORS uygun ilke ile etkinleştirildiğinde, ASP.NET Core genellikle CORS uçuş öncesi isteklerine otomatik olarak yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-293">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="0506d-294">Bazı senaryolarda durum böyle olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-294">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="0506d-295">Örneğin, [uç nokta yönlendirme ile CORS](#ecors)kullanarak.</span><span class="sxs-lookup"><span data-stu-id="0506d-295">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="0506d-296">Aşağıdaki kod, OPTIONS istekleri için uç noktaları oluşturmak için [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="0506d-296">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="0506d-297">Yukarıdaki kodun sınanma yönergeleri için [uç nokta yönlendirme ve [HttpOptions] içeren Test CORS'e](#tcer) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-297">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="0506d-298">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-298">Set the preflight expiration time</span></span>

<span data-ttu-id="0506d-299">Üstbilgi, `Access-Control-Max-Age` ön kontrol isteğine verilen yanıtın ne kadar süreyle önbelleğe alınabileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0506d-299">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="0506d-300">Bu üstbilgi ayarlamak <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>için:</span><span class="sxs-lookup"><span data-stu-id="0506d-300">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="0506d-301">CORS nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="0506d-301">How CORS works</span></span>

<span data-ttu-id="0506d-302">Bu bölümde, HTTP iletileri düzeyinde bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğine neler olduğu açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0506d-302">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="0506d-303">CORS bir güvenlik özelliği **değildir.**</span><span class="sxs-lookup"><span data-stu-id="0506d-303">CORS is **not** a security feature.</span></span> <span data-ttu-id="0506d-304">CORS, bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-304">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="0506d-305">Örneğin, kötü niyetli bir aktör sitenize karşı [Site Arası Komut Dosyası (XSS)](xref:security/cross-site-scripting) kullanabilir ve cors özellikli sitesine bilgi çalmak için bir çapraz site isteği yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-305">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="0506d-306">BIR API CORS izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-306">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="0506d-307">CORS'i uygulamak istemciye (tarayıcıya) kalmış.</span><span class="sxs-lookup"><span data-stu-id="0506d-307">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="0506d-308">Sunucu isteği yürütür ve yanıtı döndürür, bir hata döndürür ve yanıtı engeller istemcisidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-308">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="0506d-309">Örneğin, aşağıdaki araçlardan herhangi biri sunucu yanıtını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0506d-309">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="0506d-310">Fiddler</span><span class="sxs-lookup"><span data-stu-id="0506d-310">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="0506d-311">Postman</span><span class="sxs-lookup"><span data-stu-id="0506d-311">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="0506d-312">.NET Https://yayla</span><span class="sxs-lookup"><span data-stu-id="0506d-312">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="0506d-313">Adres çubuğuna URL girerek bir web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="0506d-313">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="0506d-314">Bu, bir sunucunun tarayıcıların aksi takdirde yasaklanacak bir çapraz kökenli [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteğini yürütmesine izin vermenin bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="0506d-314">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="0506d-315">CORS'siz tarayıcılar çapraz kökenli isteklerde bulunabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-315">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="0506d-316">CORS önce, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) bu kısıtlamayı atlatmak için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="0506d-316">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="0506d-317">JSONP XHR kullanmaz, yanıtı `<script>` almak için etiketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-317">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="0506d-318">Komut dosyalarıçapraz kökenli yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-318">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="0506d-319">[CORS belirtimi,](https://www.w3.org/TR/cors/) orijinler arası istekleri etkinleştiren birkaç yeni HTTP üstbilgisini tanıttı.</span><span class="sxs-lookup"><span data-stu-id="0506d-319">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="0506d-320">Bir tarayıcı CORS'i destekliyorsa, bu başlıkları çapraz başlangıç istekleri için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-320">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="0506d-321">CORS'i etkinleştirmek için özel JavaScript kodu gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0506d-321">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="0506d-322">Dağıtılan [örnekteki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [PUT test düğmesi](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="0506d-322">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="0506d-323">Aşağıda, [Değerler](https://cors3.azurewebsites.net/) test düğmesinden `https://cors1.azurewebsites.net/api/values`' e bir çapraz kaynak isteği örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0506d-323">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="0506d-324">Üstbilgi: `Origin`</span><span class="sxs-lookup"><span data-stu-id="0506d-324">The `Origin` header:</span></span>

* <span data-ttu-id="0506d-325">İstekte bulunduran sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-325">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="0506d-326">Gereklidir ve ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-326">Is required and must be different from the host.</span></span>

<span data-ttu-id="0506d-327">**Genel başlıklar**</span><span class="sxs-lookup"><span data-stu-id="0506d-327">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="0506d-328">**Yanıt üstleri**</span><span class="sxs-lookup"><span data-stu-id="0506d-328">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="0506d-329">**İstek üstbilgi**</span><span class="sxs-lookup"><span data-stu-id="0506d-329">**Request headers**</span></span>

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

<span data-ttu-id="0506d-330">İsteklerde, `OPTIONS` sunucu yanıt **üstbilgisini** `Access-Control-Allow-Origin: {allowed origin}` yanıt olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-330">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="0506d-331">Örneğin, dağıtılan [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Sil [EnableCors]](https://cors1.azurewebsites.net/test?number=2) düğmesi `OPTIONS` isteği aşağıdaki üstbilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="0506d-331">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="0506d-332">**Genel başlıklar**</span><span class="sxs-lookup"><span data-stu-id="0506d-332">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="0506d-333">**Yanıt üstleri**</span><span class="sxs-lookup"><span data-stu-id="0506d-333">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="0506d-334">**İstek üstbilgi**</span><span class="sxs-lookup"><span data-stu-id="0506d-334">**Request headers**</span></span>

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

<span data-ttu-id="0506d-335">Önceki Yanıt **üstbilgisinde,** sunucu yanıtta [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) üstbilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-335">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="0506d-336">Bu `https://cors1.azurewebsites.net` üstbilginin değeri `Origin` istekteki üstbilgiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="0506d-336">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="0506d-337"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> Çağrıldığında, `Access-Control-Allow-Origin: *`joker karakter değeri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0506d-337">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="0506d-338">`AllowAnyOrigin`herhangi bir menşe sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-338">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="0506d-339">Yanıt `Access-Control-Allow-Origin` üstbilgi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="0506d-339">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="0506d-340">Özellikle, tarayıcı isteği izin verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-340">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="0506d-341">Sunucu başarılı bir yanıt döndürse bile, tarayıcı yanıtı istemci uygulamasına sunmaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-341">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="0506d-342">SEÇENEKLER isteklerini görüntüleme</span><span class="sxs-lookup"><span data-stu-id="0506d-342">Display OPTIONS requests</span></span>

<span data-ttu-id="0506d-343">Varsayılan olarak, Chrome ve Edge tarayıcıları F12 araçlarının ağ sekmesinde OPTIONS isteklerini göstermez.</span><span class="sxs-lookup"><span data-stu-id="0506d-343">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="0506d-344">Bu tarayıcılarda OPTIONS isteklerini görüntülemek için:</span><span class="sxs-lookup"><span data-stu-id="0506d-344">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="0506d-345">`chrome://flags/#out-of-blink-cors` veya `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="0506d-345">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="0506d-346">bayrağı devre dışı kılabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-346">disable the flag.</span></span>
* <span data-ttu-id="0506d-347">Yeni -den başlatın.</span><span class="sxs-lookup"><span data-stu-id="0506d-347">restart.</span></span>

<span data-ttu-id="0506d-348">Firefox varsayılan olarak OPTIONS isteklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="0506d-348">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="0506d-349">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="0506d-349">CORS in IIS</span></span>

<span data-ttu-id="0506d-350">IIS'ye dağıtılırken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS'in Windows Kimlik Doğrulama'dan önce çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="0506d-350">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="0506d-351">Bu senaryoyu desteklemek için [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmesi ve yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0506d-351">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="0506d-352">Test CORS</span><span class="sxs-lookup"><span data-stu-id="0506d-352">Test CORS</span></span>

<span data-ttu-id="0506d-353">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) CORS test etmek için kodu vardır.</span><span class="sxs-lookup"><span data-stu-id="0506d-353">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="0506d-354">[Nasıl indirilir bakın.](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="0506d-354">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="0506d-355">Örnek Razor Pages eklenen bir API projesidir:</span><span class="sxs-lookup"><span data-stu-id="0506d-355">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="0506d-356">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)benzer bir örnek uygulamayı test etmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-356">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="0506d-357">Aşağıdaki `ValuesController` sınama için uç noktaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0506d-357">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="0506d-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet paketi tarafından sağlanır ve rota bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0506d-358">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="0506d-359">Aşağıdaki yaklaşımlardan birini kullanarak önceki örnek kodu test edin:</span><span class="sxs-lookup"><span data-stu-id="0506d-359">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="0506d-360">Dağıtılan örnek uygulamayı ' [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)da kullanın.</span><span class="sxs-lookup"><span data-stu-id="0506d-360">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="0506d-361">Örneği indirmenize gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="0506d-361">There is no need to download the sample.</span></span>
* <span data-ttu-id="0506d-362">Örneği varsayılan `dotnet run` URL'sini kullanarak `https://localhost:5001`çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0506d-362">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="0506d-363">Bir URL için 44398 olarak ayarlanmış bağlantı noktası `https://localhost:44398`ile Visual Studio örnek çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0506d-363">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="0506d-364">F12 araçları ile bir tarayıcı kullanma:</span><span class="sxs-lookup"><span data-stu-id="0506d-364">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="0506d-365">**Değerler** düğmesini seçin ve **Ağ** sekmesindeki üstbilgiincelemesi.</span><span class="sxs-lookup"><span data-stu-id="0506d-365">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="0506d-366">PUT **test** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0506d-366">Select the **PUT test** button.</span></span> <span data-ttu-id="0506d-367">SEÇENEKLER isteğini görüntülerken verilen talimatlar için SEÇENEKLER'i görüntüleme [isteklerine](#options) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-367">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="0506d-368">**PUT testi** iki istek, bir OPTIONS ön uçuş isteği ve PUT isteği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0506d-368">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="0506d-369">Başarısız **`GetValues2 [DisableCors]`** bir CORS isteğini tetiklemek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="0506d-369">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="0506d-370">Belgede belirtildiği gibi, yanıt 200 başarı döndürür, ancak CORS isteği yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-370">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="0506d-371">CORS hatasını görmek için **Konsol** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="0506d-371">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="0506d-372">Tarayıcıya bağlı olarak, aşağıdakilere benzer bir hata görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="0506d-372">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="0506d-373">`'https://cors1.azurewebsites.net/api/values/GetValues2'` Orijinden `'https://cors3.azurewebsites.net'` erişim CORS ilkesi tarafından engellendi: İstenen kaynakta 'Access-Control-Allow-Origin' başlığı bulunmamaktadır.</span><span class="sxs-lookup"><span data-stu-id="0506d-373">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="0506d-374">İhtiyaçlarınıza opak bir yanıt yanıt veriyorsa, CORS devre dışı bırakılmış kaynağı almak için istek modunu 'kors'a göre ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0506d-374">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="0506d-375">CORS özellikli uç [noktalar, curl,](https://curl.haxx.se/) [Fiddler](https://www.telerik.com/fiddler)veya [Postacı](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-375">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="0506d-376">Bir araç kullanırken, `Origin` üstbilgi tarafından belirtilen isteğin kaynağı, isteği alan ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-376">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="0506d-377">Üstbilginin değerine *cross-origin* `Origin` göre istek çapraz kökenli değilse:</span><span class="sxs-lookup"><span data-stu-id="0506d-377">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="0506d-378">CORS Middleware'in isteği işlemesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="0506d-378">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="0506d-379">CORS başlıkları yanıt olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0506d-379">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="0506d-380">Aşağıdaki komut, `curl` bir OPTIONS isteğini bilgiyle birlikte vermek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="0506d-380">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="0506d-381">Uç nokta yönlendirme ile TEST CORS ve [HttpOptions]</span><span class="sxs-lookup"><span data-stu-id="0506d-381">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="0506d-382">Cors'un şu anda kullanarak `RequireCors` uç nokta bazında etkinleştirilmesi otomatik ön uçuş [isteklerini](#apf) ***desteklemez.***</span><span class="sxs-lookup"><span data-stu-id="0506d-382">Enabling CORS on a per-endpoint basis using `RequireCors` currently does ***not*** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="0506d-383">[CORS'i etkinleştirmek için uç nokta yönlendirmeyi](#ecors)kullanan aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="0506d-383">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="0506d-384">Aşağıdaki `TodoItems1Controller` sınama için uç noktaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="0506d-384">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="0506d-385">Dağıtılan [örneğin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [test sayfasından](https://cors1.azurewebsites.net/test?number=1) önceki kodu test edin.</span><span class="sxs-lookup"><span data-stu-id="0506d-385">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="0506d-386">**[EnableCors]** ve **GET [EnableCors] düğmeleri** başarılı olur, `[EnableCors]` çünkü uç noktalar uçuş öncesi isteklere sahip ve yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-386">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="0506d-387">Diğer uç noktalar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="0506d-387">The other endpoints fails.</span></span> <span data-ttu-id="0506d-388">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) gönderdiğinden **GET** düğmesi başarısız olur:</span><span class="sxs-lookup"><span data-stu-id="0506d-388">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="0506d-389">Aşağıdakibenzer `TodoItems2Controller` uç noktaları sağlar, ancak OPTIONS isteklerini yanıtlamak için açık kod içerir:</span><span class="sxs-lookup"><span data-stu-id="0506d-389">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="0506d-390">Dağıtılan örneğin [test sayfasından](https://cors1.azurewebsites.net/test?number=2) önceki kodu test edin.</span><span class="sxs-lookup"><span data-stu-id="0506d-390">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="0506d-391">**Denetleyici** açılır listesinde Ön **Kontrol'i** seçin ve ardından **Denetleyiciyi Ayarlayın.**</span><span class="sxs-lookup"><span data-stu-id="0506d-391">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="0506d-392">`TodoItems2Controller` Uç noktalara yapılan tüm CORS çağrıları başarılı dır.</span><span class="sxs-lookup"><span data-stu-id="0506d-392">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0506d-393">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0506d-393">Additional resources</span></span>

* [<span data-ttu-id="0506d-394">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="0506d-394">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="0506d-395">IIS CORS modülü ile başlarken</span><span class="sxs-lookup"><span data-stu-id="0506d-395">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="0506d-396">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0506d-396">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0506d-397">Bu makalede, bir ASP.NET Core uygulamasında CORS'in nasıl etkinleştirilen gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="0506d-397">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="0506d-398">Tarayıcı güvenliği, bir web sayfasının web sayfasına hizmet eden den farklı bir etki alanına istekte bulunmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="0506d-398">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="0506d-399">Bu kısıtlamaya *aynı kaynak ilkesi*denir.</span><span class="sxs-lookup"><span data-stu-id="0506d-399">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="0506d-400">Aynı kaynak ilkesi, kötü amaçlı bir sitenin başka bir siteden gelen hassas verileri okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="0506d-400">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="0506d-401">Bazen, diğer sitelerin uygulamanızda çapraz orijin isteklerinde yer almasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0506d-401">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="0506d-402">Daha fazla bilgi için [Mozilla CORS makalesine](https://developer.mozilla.org/docs/Web/HTTP/CORS)bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-402">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="0506d-403">[Çapraz Kaynak Paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="0506d-403">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="0506d-404">Bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-404">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="0506d-405">Bir güvenlik özelliği **değildir,** CORS güvenliği rahatlatır.</span><span class="sxs-lookup"><span data-stu-id="0506d-405">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="0506d-406">BIR API CORS izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-406">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="0506d-407">Daha fazla bilgi için [CORS'in nasıl çalıştığını](#how-cors)görün.</span><span class="sxs-lookup"><span data-stu-id="0506d-407">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="0506d-408">Sunucunun, diğerlerini reddederken bazı kişiler arası isteklere açıkça izin vermesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-408">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="0506d-409">[JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerden daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="0506d-409">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="0506d-410">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="0506d-410">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="0506d-411">Aynı köken</span><span class="sxs-lookup"><span data-stu-id="0506d-411">Same origin</span></span>

<span data-ttu-id="0506d-412">Aynı düzenleri, ana bilgisayarlar ve bağlantı noktaları[(RFC 6454)](https://tools.ietf.org/html/rfc6454)varsa, iki URL aynı kökene sahiptir.</span><span class="sxs-lookup"><span data-stu-id="0506d-412">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="0506d-413">Bu iki URL aynı kökene sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0506d-413">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="0506d-414">Bu URL'lerin önceki iki URL'den farklı kökenleri vardır:</span><span class="sxs-lookup"><span data-stu-id="0506d-414">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="0506d-415">`https://example.net`&ndash; Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="0506d-415">`https://example.net` &ndash; Different domain</span></span>
* <span data-ttu-id="0506d-416">`https://www.example.com/foo.html`&ndash; Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="0506d-416">`https://www.example.com/foo.html` &ndash; Different subdomain</span></span>
* <span data-ttu-id="0506d-417">`http://example.com/foo.html`&ndash; Farklı şema</span><span class="sxs-lookup"><span data-stu-id="0506d-417">`http://example.com/foo.html` &ndash; Different scheme</span></span>
* <span data-ttu-id="0506d-418">`https://example.com:9000/foo.html`&ndash; Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="0506d-418">`https://example.com:9000/foo.html` &ndash; Different port</span></span>

<span data-ttu-id="0506d-419">Internet Explorer, kaynaklarını karşılaştırırken bağlantı noktasını dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-419">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="0506d-420">Adlı ilke ve middleware ile CORS</span><span class="sxs-lookup"><span data-stu-id="0506d-420">CORS with named policy and middleware</span></span>

<span data-ttu-id="0506d-421">CORS Middleware, çapraz orijin isteklerini işler.</span><span class="sxs-lookup"><span data-stu-id="0506d-421">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="0506d-422">Aşağıdaki kod, belirtilen menşee sahip tüm uygulama için CORS sağlar:</span><span class="sxs-lookup"><span data-stu-id="0506d-422">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="0506d-423">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="0506d-423">The preceding code:</span></span>

* <span data-ttu-id="0506d-424">İlke adını "myAllowSpecificOrigins"\_olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-424">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="0506d-425">İlke adı rasgele.</span><span class="sxs-lookup"><span data-stu-id="0506d-425">The policy name is arbitrary.</span></span>
* <span data-ttu-id="0506d-426">CORS'u sağlayan uzatma yöntemini <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> çağırır.</span><span class="sxs-lookup"><span data-stu-id="0506d-426">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="0506d-427"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile aramalar .</span><span class="sxs-lookup"><span data-stu-id="0506d-427">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="0506d-428">Lambda bir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> nesne alır.</span><span class="sxs-lookup"><span data-stu-id="0506d-428">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="0506d-429">[Yapılandırma seçenekleri](#cors-policy-options), `WithOrigins`örneğin, bu makalede daha sonra açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="0506d-429">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="0506d-430">Yöntem <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> çağrısı, uygulamanın servis konteynerine CORS hizmetlerini ekler:</span><span class="sxs-lookup"><span data-stu-id="0506d-430">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="0506d-431">Daha fazla bilgi için bu belgedeki [CORS ilke seçeneklerine](#cpo) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-431">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="0506d-432">Yöntem, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> aşağıdaki kodda gösterildiği gibi yöntemleri zincirleyebilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-432">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="0506d-433">Not: URL' de sondaki eğik çizgi ()`/` **içermemelidir.**</span><span class="sxs-lookup"><span data-stu-id="0506d-433">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="0506d-434">URL ile sonlandırma, `/`karşılaştırma `false` döndürür ve hiçbir üstbilgi döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0506d-434">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="0506d-435">Aşağıdaki kod, CORS Middleware aracılığıyla tüm uygulama uç noktalarına CORS ilkelerini uygular:</span><span class="sxs-lookup"><span data-stu-id="0506d-435">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="0506d-436">Not: `UseCors` önce `UseMvc`çağrılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-436">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="0506d-437">Bkz. Sayfa/denetleyici/eylem düzeyinde CORS ilkesini uygulamak için [Razor Pages, denetleyiciler ve eylem yöntemlerinde CORS'i etkinleştirin.](#ecors)</span><span class="sxs-lookup"><span data-stu-id="0506d-437">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="0506d-438">Önceki koda benzer test kodu yla ilgili talimatlar için [Test CORS'e](#test) bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-438">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="0506d-439">Özniteliklerle CORS'i etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="0506d-439">Enable CORS with attributes</span></span>

<span data-ttu-id="0506d-440">[EnableCors&rbrack; özniteliği, CORS'u küresel olarak uygulamak için bir alternatif sağlar. &lbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="0506d-440">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="0506d-441">Öznitelik, `[EnableCors]` tüm uç noktalar yerine seçilen uç noktalar için CORS'e olanak sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-441">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="0506d-442">Varsayılan `[EnableCors]` ilkeyi belirtmek `[EnableCors("{Policy String}")]` ve bir ilke belirtmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="0506d-442">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="0506d-443">Öznitelik `[EnableCors]` uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-443">The `[EnableCors]` attribute can be applied to:</span></span>

* <span data-ttu-id="0506d-444">Jilet Sayfası`PageModel`</span><span class="sxs-lookup"><span data-stu-id="0506d-444">Razor Page `PageModel`</span></span>
* <span data-ttu-id="0506d-445">Denetleyicisi</span><span class="sxs-lookup"><span data-stu-id="0506d-445">Controller</span></span>
* <span data-ttu-id="0506d-446">Denetleyici eylem yöntemi</span><span class="sxs-lookup"><span data-stu-id="0506d-446">Controller action method</span></span>

<span data-ttu-id="0506d-447">Öznitelik ile `[EnableCors]` denetleyici/sayfa modeli/eylem için farklı ilkeler uygulayabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0506d-447">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="0506d-448">Öznitelik `[EnableCors]` denetleyicilere/sayfa modeli/eylem yöntemine uygulandığında ve ara yazılımda CORS etkinleştirildiğinde, ***her iki*** ilke de uygulanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-448">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, ***both*** policies are applied.</span></span> <span data-ttu-id="0506d-449">İlkeleri ***birleştirmemenizi*** öneririz.</span><span class="sxs-lookup"><span data-stu-id="0506d-449">We recommend ***not*** combining policies.</span></span> <span data-ttu-id="0506d-450">Öznitelik veya ara yazılım kullanın, \***her ikisi de değil**. `[EnableCors]`</span><span class="sxs-lookup"><span data-stu-id="0506d-450">Use the `[EnableCors]` attribute or middleware, \***not both**.</span></span> <span data-ttu-id="0506d-451">Kullanırken, `[EnableCors]`varsayılan bir ilke **tanımlamayın.**</span><span class="sxs-lookup"><span data-stu-id="0506d-451">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="0506d-452">Aşağıdaki kod her yöntem için farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="0506d-452">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="0506d-453">Aşağıdaki kod bir CORS varsayılan ilkesi ve `"AnotherPolicy"`adlı bir ilke oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0506d-453">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="0506d-454">CORS'u devre dışı</span><span class="sxs-lookup"><span data-stu-id="0506d-454">Disable CORS</span></span>

<span data-ttu-id="0506d-455">[DisableCors&rbrack; denetleyici/sayfa modeli/eylem için CORS'u devre dışı bırakmaz. &lbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)</span><span class="sxs-lookup"><span data-stu-id="0506d-455">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="0506d-456">CORS politika seçenekleri</span><span class="sxs-lookup"><span data-stu-id="0506d-456">CORS policy options</span></span>

<span data-ttu-id="0506d-457">Bu bölümde, BIR CORS ilkesinde ayarlanabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="0506d-457">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="0506d-458">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-458">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="0506d-459">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-459">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="0506d-460">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-460">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="0506d-461">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-461">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="0506d-462">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="0506d-462">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="0506d-463">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-463">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="0506d-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>olarak `Startup.ConfigureServices`adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0506d-464"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0506d-465">Bazı seçenekler için, [önce CORS nasıl çalışır](#how-cors) bölümünü okumak yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-465">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="0506d-466">İzin verilen kökenleri ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-466">Set the allowed origins</span></span>

<span data-ttu-id="0506d-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash; Herhangi bir şema (veya)`http` `https`ile tüm kökenlerden CORS isteklerini sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-467"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="0506d-468">`AllowAnyOrigin`herhangi bir *web sitesi* uygulamaya çapraz orijin isteklerinde bulunabileceğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-468">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="0506d-469">Belirtilmesi `AllowAnyOrigin` `AllowCredentials` ve güvenli olmayan bir yapılandırmadır ve site ler arası istek sahteciliğiyle sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-469">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="0506d-470">Güvenli bir uygulama için, istemcinin sunucu kaynaklarına erişmek için kendisini yetkilendirmesi gerekiyorsa, tam bir kaynak listesi belirtin.</span><span class="sxs-lookup"><span data-stu-id="0506d-470">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="0506d-471">`AllowAnyOrigin`ön kontrol isteklerini `Access-Control-Allow-Origin` ve üstbilgietkiler.</span><span class="sxs-lookup"><span data-stu-id="0506d-471">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="0506d-472">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-472">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0506d-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash; İlkenin <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> özelliğini, kaynağın izin verilip verilmediğini değerlendirirken, kökenlerin yapılandırılmış bir joker etki alanıyla eşleşmesine olanak tanıyan bir işlev olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="0506d-474">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-474">Set the allowed HTTP methods</span></span>

<span data-ttu-id="0506d-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="0506d-475"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="0506d-476">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="0506d-476">Allows any HTTP method:</span></span>
* <span data-ttu-id="0506d-477">Ön kontrol isteklerini `Access-Control-Allow-Methods` ve üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="0506d-477">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="0506d-478">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="0506d-479">İzin verilen istek üstbilgisini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-479">Set the allowed request headers</span></span>

<span data-ttu-id="0506d-480">Belirli üstbilginin CORS isteğinde gönderilmesine izin vermek için <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> yazar *istek üstbilgisini*arayın, arayın ve izin verilen üstbilgibelirtin:</span><span class="sxs-lookup"><span data-stu-id="0506d-480">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0506d-481">Tüm yazar istek üstbilgilerine <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="0506d-481">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0506d-482">Bu ayar, ön kontrol `Access-Control-Request-Headers` isteklerini ve üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="0506d-482">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="0506d-483">Daha fazla bilgi için [Ön Uçuş istekleri](#preflight-requests) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-483">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="0506d-484">CORS Middleware her zaman CorsPolicy.Headers yapılandırılan değerlerne bakılmaksızın gönderilmesi `Access-Control-Request-Headers` için dört üstbilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-484">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="0506d-485">Bu üstbilgi listesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="0506d-485">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="0506d-486">Örneğin, aşağıdaki gibi yapılandırılan bir uygulamayı düşünün:</span><span class="sxs-lookup"><span data-stu-id="0506d-486">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="0506d-487">CORS Middleware, her zaman beyaz listeye alındığı için ön `Content-Language` kontrol isteğine aşağıdaki istek üstbilgisiyle başarıyla yanıt verir:</span><span class="sxs-lookup"><span data-stu-id="0506d-487">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="0506d-488">Açıkta kalan yanıt üstaylarını ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-488">Set the exposed response headers</span></span>

<span data-ttu-id="0506d-489">Varsayılan olarak, tarayıcı tüm yanıt üstbilgisini uygulamaya maruz bırakmaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-489">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="0506d-490">Daha fazla bilgi için [W3C Cross-Origin Kaynak Paylaşımı (Terminoloji): Basit Yanıt Üstbilgisi'ne](https://www.w3.org/TR/cors/#simple-response-header)bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-490">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="0506d-491">Varsayılan olarak kullanılabilir yanıt üstbilgi:</span><span class="sxs-lookup"><span data-stu-id="0506d-491">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="0506d-492">CORS belirtimi bu üstbilgi *basit yanıt üstbilgi çağırır.*</span><span class="sxs-lookup"><span data-stu-id="0506d-492">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="0506d-493">Diğer üstbilgileri uygulamaiçin kullanılabilir <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>hale getirmek için aşağıdakileri arayın:</span><span class="sxs-lookup"><span data-stu-id="0506d-493">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="0506d-494">Orijinler arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="0506d-494">Credentials in cross-origin requests</span></span>

<span data-ttu-id="0506d-495">Kimlik bilgileri, CORS isteğinde özel kullanım gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0506d-495">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="0506d-496">Varsayılan olarak, tarayıcı bir çapraz kaynak isteği ile kimlik bilgileri göndermez.</span><span class="sxs-lookup"><span data-stu-id="0506d-496">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="0506d-497">Kimlik bilgileri çerezleri ve HTTP kimlik doğrulama şemalarını içerir.</span><span class="sxs-lookup"><span data-stu-id="0506d-497">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="0506d-498">Bir çapraz kaynak isteği ile kimlik bilgileri `XMLHttpRequest.withCredentials` göndermek `true`için, istemci nin .</span><span class="sxs-lookup"><span data-stu-id="0506d-498">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="0506d-499">Doğrudan `XMLHttpRequest` kullanma:</span><span class="sxs-lookup"><span data-stu-id="0506d-499">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="0506d-500">jQuery kullanma:</span><span class="sxs-lookup"><span data-stu-id="0506d-500">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="0506d-501">Getir [API'sini](https://developer.mozilla.org/docs/Web/API/Fetch_API)kullanma :</span><span class="sxs-lookup"><span data-stu-id="0506d-501">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="0506d-502">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-502">The server must allow the credentials.</span></span> <span data-ttu-id="0506d-503">Orijinler arası kimlik <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>bilgilerine izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="0506d-503">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="0506d-504">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgilerine izin verdiğini söyleyen bir üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="0506d-504">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="0506d-505">Tarayıcı kimlik bilgileri gönderir, ancak yanıt geçerli `Access-Control-Allow-Credentials` bir üstbilgi içermiyorsa, tarayıcı uygulamaya yanıtı açıklamaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="0506d-505">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="0506d-506">Orjinal kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-506">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="0506d-507">Başka bir etki alanında bulunan bir web sitesi, kullanıcının bilgisi olmadan kullanıcı adına oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-507">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="0506d-508">CORS belirtimi, üstbilgi `"*"` varsa(tüm kökenlerin) kaynağını ayarlamanın `Access-Control-Allow-Credentials` geçersiz olduğunu da belirtir.</span><span class="sxs-lookup"><span data-stu-id="0506d-508">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="0506d-509">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="0506d-509">Preflight requests</span></span>

<span data-ttu-id="0506d-510">Bazı CORS istekleri için tarayıcı, gerçek isteği yapmadan önce ek bir istek gönderir.</span><span class="sxs-lookup"><span data-stu-id="0506d-510">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="0506d-511">Bu istek *bir ön kontrol isteği*olarak adlandırılır.</span><span class="sxs-lookup"><span data-stu-id="0506d-511">This request is called a *preflight request*.</span></span> <span data-ttu-id="0506d-512">Aşağıdaki koşullar doğruysa, tarayıcı ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-512">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="0506d-513">İstek yöntemi GET, HEAD veya POST'dur.</span><span class="sxs-lookup"><span data-stu-id="0506d-513">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="0506d-514">Uygulama, istek `Accept`üstbilgisini , , `Accept-Language` `Content-Language`, `Content-Type`veya `Last-Event-ID`.</span><span class="sxs-lookup"><span data-stu-id="0506d-514">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="0506d-515">Üstbilgi, `Content-Type` küme, aşağıdaki değerlerden birine sahiptir:</span><span class="sxs-lookup"><span data-stu-id="0506d-515">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="0506d-516">İstemci isteği için ayarlanan istek üstbilgilerine ilişkin kural, `setRequestHeader` `XMLHttpRequest` uygulamanın nesneyi çağırarak ayarladığını üstbilgi için geçerlidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-516">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="0506d-517">CORS belirtimi bu üstbilgi *yazar istek üstbilgi*çağırır.</span><span class="sxs-lookup"><span data-stu-id="0506d-517">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="0506d-518">Kural, tarayıcının ayarlayabileceği `User-Agent`, , `Host`veya `Content-Length`.</span><span class="sxs-lookup"><span data-stu-id="0506d-518">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="0506d-519">Aşağıda, ön kontrol isteğine bir örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0506d-519">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="0506d-520">Uçuş öncesi istek HTTP OPTIONS yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-520">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="0506d-521">İki özel üstbilgi içerir:</span><span class="sxs-lookup"><span data-stu-id="0506d-521">It includes two special headers:</span></span>

* <span data-ttu-id="0506d-522">`Access-Control-Request-Method`: Gerçek istek için kullanılacak HTTP yöntemi.</span><span class="sxs-lookup"><span data-stu-id="0506d-522">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="0506d-523">`Access-Control-Request-Headers`: Uygulamanın gerçek istek üzerine ayarladığının istek üstbilgilerinin listesi.</span><span class="sxs-lookup"><span data-stu-id="0506d-523">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="0506d-524">Daha önce belirtildiği gibi, `User-Agent`bu, tarayıcının belirlediği üstleri içermez.</span><span class="sxs-lookup"><span data-stu-id="0506d-524">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="0506d-525">CORS uygun ilke ile etkinleştirildiğinde, ASP.NET Core genellikle CORS uçuş öncesi isteklerine otomatik olarak yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-525">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="0506d-526">[Ön uçuş istekleri için [HttpOptions] özniteliğine](#pro)bakın.</span><span class="sxs-lookup"><span data-stu-id="0506d-526">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="0506d-527">CORS ön kontrol isteği, `Access-Control-Request-Headers` sunucuya gerçek istekle birlikte gönderilen üstbilgileri gösteren bir üstbilgi içerebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-527">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="0506d-528">Belirli üstbilgi için: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*></span><span class="sxs-lookup"><span data-stu-id="0506d-528">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="0506d-529">Tüm yazar istek üstbilgilerine <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>izin vermek için:</span><span class="sxs-lookup"><span data-stu-id="0506d-529">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="0506d-530">Tarayıcılar, nasıl ayarlandıkları `Access-Control-Request-Headers`konusunda tamamen tutarlı değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-530">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="0506d-531">Üstbilginizi (veya kullanım) `"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>dışında başka bir şeye `Accept`ayarlarsanız, en az , `Content-Type`ve `Origin`, artı desteklemek istediğiniz özel üstbilgi içermelidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-531">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="0506d-532">Aşağıda, ön kontrol isteğine örnek bir yanıt verilmiştir (sunucunun isteğe izin verdiğini varsayarsak):</span><span class="sxs-lookup"><span data-stu-id="0506d-532">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="0506d-533">Yanıt, izin `Access-Control-Allow-Methods` verilen yöntemleri ve isteğe bağlı `Access-Control-Allow-Headers` olarak izin verilen üstbilgilisteleyen bir üstbilgi içeren bir üstbilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="0506d-533">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="0506d-534">Ön kontrol isteği başarılı olursa, tarayıcı gerçek isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="0506d-534">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="0506d-535">Ön kontrol isteği reddedilirse, uygulama *200 Tamam* yanıtını döndürür, ancak CORS üstbilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="0506d-535">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="0506d-536">Bu nedenle, tarayıcı çapraz kaynak isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="0506d-536">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="0506d-537">Uçuş öncesi son kullanma süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="0506d-537">Set the preflight expiration time</span></span>

<span data-ttu-id="0506d-538">Üstbilgi, `Access-Control-Max-Age` ön kontrol isteğine verilen yanıtın ne kadar süreyle önbelleğe alınabileceğini belirtir.</span><span class="sxs-lookup"><span data-stu-id="0506d-538">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="0506d-539">Bu üstbilgi ayarlamak <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>için:</span><span class="sxs-lookup"><span data-stu-id="0506d-539">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="0506d-540">CORS nasıl çalışır?</span><span class="sxs-lookup"><span data-stu-id="0506d-540">How CORS works</span></span>

<span data-ttu-id="0506d-541">Bu bölümde, HTTP iletileri düzeyinde bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğine neler olduğu açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0506d-541">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="0506d-542">CORS bir güvenlik özelliği **değildir.**</span><span class="sxs-lookup"><span data-stu-id="0506d-542">CORS is **not** a security feature.</span></span> <span data-ttu-id="0506d-543">CORS, bir sunucunun aynı orijin ilkesini gevşetmesine olanak tanıyan bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-543">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="0506d-544">Örneğin, kötü niyetli bir aktör sitenize karşı [Site Ötesi Komut Dosyasını (XSS) engelleyebilir](xref:security/cross-site-scripting) ve CORS özellikli sitesine bilgi çalmak için bir çapraz site isteği gerçekleştirebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-544">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="0506d-545">CORS'e izin vererek API'niz daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="0506d-545">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="0506d-546">CORS'i uygulamak istemciye (tarayıcıya) kalmış.</span><span class="sxs-lookup"><span data-stu-id="0506d-546">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="0506d-547">Sunucu isteği yürütür ve yanıtı döndürür, bir hata döndürür ve yanıtı engeller istemcisidir.</span><span class="sxs-lookup"><span data-stu-id="0506d-547">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="0506d-548">Örneğin, aşağıdaki araçlardan herhangi biri sunucu yanıtını görüntüler:</span><span class="sxs-lookup"><span data-stu-id="0506d-548">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="0506d-549">Fiddler</span><span class="sxs-lookup"><span data-stu-id="0506d-549">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="0506d-550">Postman</span><span class="sxs-lookup"><span data-stu-id="0506d-550">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="0506d-551">.NET Https://yayla</span><span class="sxs-lookup"><span data-stu-id="0506d-551">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="0506d-552">Adres çubuğuna URL girerek bir web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="0506d-552">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="0506d-553">Bu, bir sunucunun tarayıcıların aksi takdirde yasaklanacak bir çapraz kökenli [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteğini yürütmesine izin vermenin bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="0506d-553">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="0506d-554">Tarayıcılar (CORS olmadan) çapraz kökenli isteklerde olamaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-554">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="0506d-555">CORS önce, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) bu kısıtlamayı atlatmak için kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="0506d-555">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="0506d-556">JSONP XHR kullanmaz, yanıtı `<script>` almak için etiketi kullanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-556">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="0506d-557">Komut dosyalarıçapraz kökenli yüklenebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-557">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="0506d-558">[CORS belirtimi,](https://www.w3.org/TR/cors/) orijinler arası istekleri etkinleştiren birkaç yeni HTTP üstbilgisini tanıttı.</span><span class="sxs-lookup"><span data-stu-id="0506d-558">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="0506d-559">Bir tarayıcı CORS'i destekliyorsa, bu başlıkları çapraz başlangıç istekleri için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-559">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="0506d-560">CORS'i etkinleştirmek için özel JavaScript kodu gerekmez.</span><span class="sxs-lookup"><span data-stu-id="0506d-560">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="0506d-561">Aşağıda, bir çapraz kaynak isteği örneği verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0506d-561">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="0506d-562">Üstbilgi, `Origin` isteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-562">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="0506d-563">Üstbilgi `Origin` gereklidir ve ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-563">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="0506d-564">Sunucu isteğe izin veriyorsa, yanıttaki üstbilgiyi `Access-Control-Allow-Origin` ayarlar.</span><span class="sxs-lookup"><span data-stu-id="0506d-564">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="0506d-565">Bu üstbilginin değeri, `Origin` istekteki üstbilgiyle eşleşir veya `"*"`joker karakter değeridir, yani herhangi bir menşee izin verilir:</span><span class="sxs-lookup"><span data-stu-id="0506d-565">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="0506d-566">Yanıt `Access-Control-Allow-Origin` üstbilgi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="0506d-566">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="0506d-567">Özellikle, tarayıcı isteği izin verir.</span><span class="sxs-lookup"><span data-stu-id="0506d-567">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="0506d-568">Sunucu başarılı bir yanıt döndürse bile, tarayıcı yanıtı istemci uygulamasına sunmaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-568">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="0506d-569">Test CORS</span><span class="sxs-lookup"><span data-stu-id="0506d-569">Test CORS</span></span>

<span data-ttu-id="0506d-570">CORS'u test etmek için:</span><span class="sxs-lookup"><span data-stu-id="0506d-570">To test CORS:</span></span>

1. <span data-ttu-id="0506d-571">[Bir API projesi oluşturun.](xref:tutorials/first-web-api)</span><span class="sxs-lookup"><span data-stu-id="0506d-571">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="0506d-572">Alternatif olarak, [örnek indirebilirsiniz.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)</span><span class="sxs-lookup"><span data-stu-id="0506d-572">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="0506d-573">Bu belgedeki yaklaşımlardan birini kullanarak CORS'i etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="0506d-573">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="0506d-574">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="0506d-574">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="0506d-575">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)benzer bir örnek uygulamayı test etmek için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-575">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="0506d-576">Bir web uygulaması projesi (Razor Pages veya MVC) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0506d-576">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="0506d-577">Örnekte Razor Pages kullanır.</span><span class="sxs-lookup"><span data-stu-id="0506d-577">The sample uses Razor Pages.</span></span> <span data-ttu-id="0506d-578">Web uygulamasını API projesiyle aynı çözümde oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="0506d-578">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="0506d-579">*Index.cshtml* dosyasına aşağıdaki vurgulanmış kodu ekleyin:</span><span class="sxs-lookup"><span data-stu-id="0506d-579">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="0506d-580">Önceki kodda, dağıtılan `url: 'https://<web app>.azurewebsites.net/api/values/1',` uygulamanın URL'si ile değiştirin.</span><span class="sxs-lookup"><span data-stu-id="0506d-580">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="0506d-581">API projesini dağıtın.</span><span class="sxs-lookup"><span data-stu-id="0506d-581">Deploy the API project.</span></span> <span data-ttu-id="0506d-582">Örneğin, [Azure'a dağıtın.](xref:host-and-deploy/azure-apps/index)</span><span class="sxs-lookup"><span data-stu-id="0506d-582">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="0506d-583">Masaüstünden Razor Pages veya MVC uygulamasını çalıştırın ve **Test** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="0506d-583">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="0506d-584">Hata iletilerini gözden geçirmek için F12 araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="0506d-584">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="0506d-585">Yerel ana bilgisayar `WithOrigins` kaynağını kaldırın ve uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="0506d-585">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="0506d-586">Alternatif olarak, istemci uygulamasını farklı bir bağlantı noktasıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0506d-586">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="0506d-587">Örneğin, Visual Studio çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="0506d-587">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="0506d-588">İstemci uygulaması yla test edin.</span><span class="sxs-lookup"><span data-stu-id="0506d-588">Test with the client app.</span></span> <span data-ttu-id="0506d-589">CORS hataları bir hata döndürer, ancak hata iletisi JavaScript için kullanılamaz.</span><span class="sxs-lookup"><span data-stu-id="0506d-589">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="0506d-590">Hatayı görmek için F12 araçlarındaki konsol sekmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0506d-590">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="0506d-591">Tarayıcıya bağlı olarak, aşağıdakilere benzer bir hata (F12 araçları konsolunda) alırsınız:</span><span class="sxs-lookup"><span data-stu-id="0506d-591">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="0506d-592">Microsoft Edge'i kullanma:</span><span class="sxs-lookup"><span data-stu-id="0506d-592">Using Microsoft Edge:</span></span>

     <span data-ttu-id="0506d-593">**SEC7120: [CORS] `https://localhost:44375` Menşei, erişim kaynağı kaynağı için Access-Control-Allow-Origin yanıt `https://localhost:44375` başlığında`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="0506d-593">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="0506d-594">Chrome'u kullanma:</span><span class="sxs-lookup"><span data-stu-id="0506d-594">Using Chrome:</span></span>

     <span data-ttu-id="0506d-595">**XMLHttpRequest'e `https://webapi.azurewebsites.net/api/values/1` orijinli `https://localhost:44375` erişim CORS ilkesi tarafından engellendi: İstenen kaynakta 'Access-Control-Allow-Origin' başlığı bulunmamaktadır.**</span><span class="sxs-lookup"><span data-stu-id="0506d-595">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="0506d-596">CORS özellikli uç [noktalar, Kemancı](https://www.telerik.com/fiddler) veya [Postacı](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="0506d-596">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="0506d-597">Bir araç kullanırken, `Origin` üstbilgi tarafından belirtilen isteğin kaynağı, isteği alan ana bilgisayardan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0506d-597">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="0506d-598">Üstbilginin değerine *cross-origin* `Origin` göre istek çapraz kökenli değilse:</span><span class="sxs-lookup"><span data-stu-id="0506d-598">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="0506d-599">CORS Middleware'in isteği işlemesine gerek yoktur.</span><span class="sxs-lookup"><span data-stu-id="0506d-599">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="0506d-600">CORS başlıkları yanıt olarak döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0506d-600">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="0506d-601">CORS in IIS</span><span class="sxs-lookup"><span data-stu-id="0506d-601">CORS in IIS</span></span>

<span data-ttu-id="0506d-602">IIS'ye dağıtılırken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS'in Windows Kimlik Doğrulama'dan önce çalışması gerekiyor.</span><span class="sxs-lookup"><span data-stu-id="0506d-602">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="0506d-603">Bu senaryoyu desteklemek için [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmesi ve yapılandırılması gerekir.</span><span class="sxs-lookup"><span data-stu-id="0506d-603">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0506d-604">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0506d-604">Additional resources</span></span>

* [<span data-ttu-id="0506d-605">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="0506d-605">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="0506d-606">IIS CORS modülü ile başlarken</span><span class="sxs-lookup"><span data-stu-id="0506d-606">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
