---
<span data-ttu-id="fcc74-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="fcc74-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="fcc74-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="fcc74-102">'Blazor'</span></span>
- <span data-ttu-id="fcc74-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="fcc74-103">'Identity'</span></span>
- <span data-ttu-id="fcc74-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="fcc74-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="fcc74-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="fcc74-105">'Razor'</span></span>
- <span data-ttu-id="fcc74-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="fcc74-106">'SignalR' uid:</span></span> 

---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a><span data-ttu-id="fcc74-107">ASP.NET Core 'de çıkış noktaları arası Istekleri (CORS) etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="fcc74-107">Enable Cross-Origin Requests (CORS) in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fcc74-108">Tarafından [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Kirk larkabağı](https://twitter.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="fcc74-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Kirk Larkin](https://twitter.com/serpent5)</span></span>

<span data-ttu-id="fcc74-109">Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-109">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="fcc74-110">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="fcc74-110">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="fcc74-111">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-111">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="fcc74-112">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="fcc74-112">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="fcc74-113">Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-113">Sometimes, you might want to allow other sites to make cross-origin requests to your app.</span></span> <span data-ttu-id="fcc74-114">Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="fcc74-114">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="fcc74-115">[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="fcc74-115">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="fcc74-116">, Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-116">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="fcc74-117">Güvenlik özelliği **değil** , CORS güvenliği.</span><span class="sxs-lookup"><span data-stu-id="fcc74-117">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="fcc74-118">CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-118">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="fcc74-119">Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="fcc74-119">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="fcc74-120">Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-120">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="fcc74-121">, [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-121">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="fcc74-122">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fcc74-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="fcc74-123">Aynı kaynak</span><span class="sxs-lookup"><span data-stu-id="fcc74-123">Same origin</span></span>

<span data-ttu-id="fcc74-124">Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="fcc74-124">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="fcc74-125">Bu iki URL aynı kaynağa sahiptir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-125">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="fcc74-126">Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-126">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="fcc74-127">`https://example.net`: Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="fcc74-127">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="fcc74-128">`https://www.example.com/foo.html`: Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="fcc74-128">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="fcc74-129">`http://example.com/foo.html`: Farklı düzen</span><span class="sxs-lookup"><span data-stu-id="fcc74-129">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="fcc74-130">`https://example.com:9000/foo.html`: Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="fcc74-130">`https://example.com:9000/foo.html`: Different port</span></span>

## <a name="enable-cors"></a><span data-ttu-id="fcc74-131">CORS'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="fcc74-131">Enable CORS</span></span>

<span data-ttu-id="fcc74-132">CORS 'yi etkinleştirmenin üç yolu vardır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-132">There are three ways to enable CORS:</span></span>

* <span data-ttu-id="fcc74-133">[Adlandırılmış bir ilke](#np) veya [varsayılan ilke](#dp)kullanan ara yazılımlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-133">In middleware using a [named policy](#np) or [default policy](#dp).</span></span>
* <span data-ttu-id="fcc74-134">[Uç nokta yönlendirme](#ecors)kullanılıyor.</span><span class="sxs-lookup"><span data-stu-id="fcc74-134">Using [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="fcc74-135">[[Enablecors]](#attr) özniteliğiyle birlikte.</span><span class="sxs-lookup"><span data-stu-id="fcc74-135">With the [[EnableCors]](#attr) attribute.</span></span>

<span data-ttu-id="fcc74-136">Adlandırılmış ilkeyle [[Enablecors]](#attr) ÖZNITELIĞINI kullanmak CORS 'yi destekleyen uç noktaları sınırlayan en yoğun denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-136">Using the [[EnableCors]](#attr) attribute with a named policy provides the finest control in limiting endpoints that support CORS.</span></span>

<span data-ttu-id="fcc74-137">Her yaklaşım aşağıdaki bölümlerde ayrıntılı olarak verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-137">Each approach is detailed in the following sections.</span></span>

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="fcc74-138">Adlandırılmış ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-138">CORS with named policy and middleware</span></span>

<span data-ttu-id="fcc74-139">CORS ara yazılımı, çıkış noktaları arası istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-139">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="fcc74-140">Aşağıdaki kod, belirtilen kaynakları kullanarak tüm uygulamanın uç noktalarına bir CORS ilkesi uygular:</span><span class="sxs-lookup"><span data-stu-id="fcc74-140">The following code applies a CORS policy to all the app's endpoints with the specified origins:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

<span data-ttu-id="fcc74-141">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="fcc74-141">The preceding code:</span></span>

* <span data-ttu-id="fcc74-142">İlke adını olarak ayarlar `_myAllowSpecificOrigins` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-142">Sets the policy name to `_myAllowSpecificOrigins`.</span></span> <span data-ttu-id="fcc74-143">İlke adı rastgele olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-143">The policy name is arbitrary.</span></span>
* <span data-ttu-id="fcc74-144"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>Uzantı yöntemini çağırır ve `_myAllowSpecificOrigins` CORS ilkesini belirtir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-144">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method and specifies the  `_myAllowSpecificOrigins` CORS policy.</span></span> <span data-ttu-id="fcc74-145">`UseCors`CORS ara yazılımını ekler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-145">`UseCors` adds the CORS middleware.</span></span> <span data-ttu-id="fcc74-146">Çağrısının `UseCors` sonra `UseRouting` , öncesi yerleştirilmesi gerekir `UseAuthorization` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-146">The call to `UseCors` must be placed after `UseRouting`, but before `UseAuthorization`.</span></span> <span data-ttu-id="fcc74-147">Daha fazla bilgi için bkz. [Ara yazılım sırası](xref:fundamentals/middleware/index#middleware-order).</span><span class="sxs-lookup"><span data-stu-id="fcc74-147">For more information, see [Middleware order](xref:fundamentals/middleware/index#middleware-order).</span></span>
* <span data-ttu-id="fcc74-148"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-148">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="fcc74-149">Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> .</span><span class="sxs-lookup"><span data-stu-id="fcc74-149">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="fcc74-150">Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-150">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>
* <span data-ttu-id="fcc74-151">`_myAllowSpecificOrigins`Tüm denetleyici uç noktaları için CORS ilkesini etkinleştirilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-151">Enables the `_myAllowSpecificOrigins` CORS policy for all controller endpoints.</span></span> <span data-ttu-id="fcc74-152">Belirli uç noktalara bir CORS ilkesi uygulamak için bkz. [Endpoint Routing](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-152">See [endpoint routing](#ecors) to apply a CORS policy to specific endpoints.</span></span>

<span data-ttu-id="fcc74-153">Endpoint Routing ile **CORS ara yazılımı** , ve çağrıları arasında yürütülecek şekilde yapılandırılmalıdır `UseRouting` `UseEndpoints` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-153">With endpoint routing, the CORS middleware **must** be configured to execute between the calls to `UseRouting` and `UseEndpoints`.</span></span>

<span data-ttu-id="fcc74-154">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-154">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<span data-ttu-id="fcc74-155"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:</span><span class="sxs-lookup"><span data-stu-id="fcc74-155">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="fcc74-156">Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-156">For more information, see [CORS policy options](#cpo) in this document.</span></span>

<span data-ttu-id="fcc74-157"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemler aşağıdaki kodda gösterildiği gibi zincirleme olabilir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-157">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> methods can be chained, as shown in the following code:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

<span data-ttu-id="fcc74-158">Not: belirtilen URL sonunda eğik **not** çizgi ( `/` ) içermemelidir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-158">Note: The specified URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="fcc74-159">URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-159">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a><span data-ttu-id="fcc74-160">Varsayılan ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-160">CORS with default policy and middleware</span></span>

<span data-ttu-id="fcc74-161">Aşağıdaki vurgulanan kod varsayılan CORS ilkesini sunar:</span><span class="sxs-lookup"><span data-stu-id="fcc74-161">The following highlighted code enables the default CORS policy:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

<span data-ttu-id="fcc74-162">Yukarıdaki kod, tüm denetleyici uç noktalarına varsayılan CORS ilkesini uygular.</span><span class="sxs-lookup"><span data-stu-id="fcc74-162">The preceding code applies the default CORS policy to all controller endpoints.</span></span>

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a><span data-ttu-id="fcc74-163">Uç nokta yönlendirme ile CORS 'yi etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="fcc74-163">Enable Cors with endpoint routing</span></span>

<span data-ttu-id="fcc74-164">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** .</span><span class="sxs-lookup"><span data-stu-id="fcc74-164">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="fcc74-165">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/aspnetcore/issues/20709) bakın ve [uç nokta yönlendirme ve [HttpOptions] ile test CORS 'yi test](#tcer)edin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-165">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/20709) and [Test CORS with endpoint routing and [HttpOptions]](#tcer).</span></span>

<span data-ttu-id="fcc74-166">Uç nokta yönlendirmesinde, CORS uzantı yöntemleri kümesi kullanılarak uç nokta temelinde etkinleştirilebilir <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-166">With endpoint routing, CORS can be enabled on a per-endpoint basis using the <xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*> set of extension methods:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

<span data-ttu-id="fcc74-167">Yukarıdaki kodda:</span><span class="sxs-lookup"><span data-stu-id="fcc74-167">In the preceding code:</span></span>

* <span data-ttu-id="fcc74-168">`app.UseCors`CORS ara yazılımını sunar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-168">`app.UseCors` enables the CORS middleware.</span></span> <span data-ttu-id="fcc74-169">Varsayılan bir ilke yapılandırılmadığından, `app.UseCors()` tek BAŞıNA CORS 'yi etkinleştirmeyin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-169">Because a default policy hasn't been configured, `app.UseCors()` alone doesn't enable CORS.</span></span>
* <span data-ttu-id="fcc74-170">`/echo`Ve denetleyici uç noktaları, belirtilen ilkeyi kullanarak çıkış noktaları arası isteklere izin verir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-170">The `/echo` and controller endpoints allow cross-origin requests using the specified policy.</span></span>
* <span data-ttu-id="fcc74-171">`/echo2` Razor Varsayılan ilke belirtilmediğinden ve sayfa uç noktaları, çıkış noktaları arası isteklere izin **vermez** .</span><span class="sxs-lookup"><span data-stu-id="fcc74-171">The `/echo2` and Razor Pages endpoints do **not** allow cross-origin requests because no default policy was specified.</span></span>

<span data-ttu-id="fcc74-172">[[Disablecors]](#dc) özniteliği, ile Endpoint Routing tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** `RequireCors` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-172">The [[DisableCors]](#dc) attribute does **not**  disable CORS that has been enabled by endpoint routing with `RequireCors`.</span></span>

<span data-ttu-id="fcc74-173">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-173">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing code similar to the preceding.</span></span>

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="fcc74-174">CORS 'yi özniteliklerle etkinleştir</span><span class="sxs-lookup"><span data-stu-id="fcc74-174">Enable CORS with attributes</span></span>

<span data-ttu-id="fcc74-175">CORS 'yi [[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliğiyle etkinleştirmek ve yalnızca CORS gerektiren uç noktalara adlandırılmış bir ilke uygulamak, en derin denetimi sağlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-175">Enabling CORS with the [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute and applying a named policy to only those endpoints that require CORS provides the finest control.</span></span>

<span data-ttu-id="fcc74-176">[[Enablecors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) ÖZNITELIĞI, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-176">The [[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="fcc74-177">`[EnableCors]`Öznitelik, tüm uç noktalar yerine seçili uç noktalar IÇIN CORS 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="fcc74-177">The `[EnableCors]` attribute enables CORS for selected endpoints, rather than all endpoints:</span></span>

* <span data-ttu-id="fcc74-178">`[EnableCors]`varsayılan ilkeyi belirtir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-178">`[EnableCors]` specifies the default policy.</span></span>
* <span data-ttu-id="fcc74-179">`[EnableCors("{Policy String}")]`adlandırılmış bir ilke belirtir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-179">`[EnableCors("{Policy String}")]` specifies a named policy.</span></span>

<span data-ttu-id="fcc74-180">`[EnableCors]`Özniteliği öğesine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-180">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="fcc74-181">Sayfasında`PageModel`</span><span class="sxs-lookup"><span data-stu-id="fcc74-181"> Page `PageModel`</span></span>
* <span data-ttu-id="fcc74-182">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="fcc74-182">Controller</span></span>
* <span data-ttu-id="fcc74-183">Denetleyici eylemi yöntemi</span><span class="sxs-lookup"><span data-stu-id="fcc74-183">Controller action method</span></span>

<span data-ttu-id="fcc74-184">Öznitelikleri olan denetleyicilere, sayfa modellerine veya eylem yöntemlerine farklı ilkeler uygulanabilir `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-184">Different policies can be applied to controllers, page models, or action methods with the `[EnableCors]` attribute.</span></span> <span data-ttu-id="fcc74-185">`[EnableCors]`Öznitelik bir denetleyiciye, sayfa modeline veya eylem yöntemine uygulandığında ve bir ara yazılım IÇINDE CORS etkinleştirildiğinde, **her iki ilke de** uygulanır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-185">When the `[EnableCors]` attribute is applied to a controller, page model, or action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="fcc74-186">\*\*İlkelerin birleştirilmesi önerilir. \*\* `[EnableCors]` **Aynı uygulamada değil, özniteliğini veya ara yazılımını kullanın.**</span><span class="sxs-lookup"><span data-stu-id="fcc74-186">**We recommend against combining policies. Use the** `[EnableCors]` **attribute or middleware, not both in the same app.**</span></span>

<span data-ttu-id="fcc74-187">Aşağıdaki kod her bir yönteme farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="fcc74-187">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="fcc74-188">Aşağıdaki kod iki CORS ilkesi oluşturur:</span><span class="sxs-lookup"><span data-stu-id="fcc74-188">The following code creates two CORS policies:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

<span data-ttu-id="fcc74-189">CORS isteklerini sınırlayan en yoğun denetim için:</span><span class="sxs-lookup"><span data-stu-id="fcc74-189">For the finest control of limiting CORS requests:</span></span>

* <span data-ttu-id="fcc74-190">`[EnableCors("MyPolicy")]`Adlandırılmış ilkeyle kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-190">Use `[EnableCors("MyPolicy")]` with a named policy.</span></span>
* <span data-ttu-id="fcc74-191">Varsayılan bir ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-191">Don't define a default policy.</span></span>
* <span data-ttu-id="fcc74-192">[Endpoint Routing](#ecors)kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-192">Don't use [endpoint routing](#ecors).</span></span>

<span data-ttu-id="fcc74-193">Sonraki bölümdeki kod, önceki listeyi karşılar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-193">The code in the next section meets the preceding list.</span></span>

<span data-ttu-id="fcc74-194">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-194">See [Test CORS](#testc) for instructions on testing code similar to the preceding code.</span></span>

<a name="dc"></a>

### <a name="disable-cors"></a><span data-ttu-id="fcc74-195">CORS 'yi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="fcc74-195">Disable CORS</span></span>

<span data-ttu-id="fcc74-196">[[Disablecors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği [Endpoint ROUTING](#ecors)tarafından etkinleştirilen CORS 'yi devre dışı **bırakmıyor** .</span><span class="sxs-lookup"><span data-stu-id="fcc74-196">The [[DisableCors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute does **not**  disable CORS that has been enabled by [endpoint routing](#ecors).</span></span>

<span data-ttu-id="fcc74-197">Aşağıdaki kod CORS ilkesini tanımlar `"MyPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="fcc74-197">The following code defines the CORS policy `"MyPolicy"`:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

<span data-ttu-id="fcc74-198">Aşağıdaki kod, eylem için CORS 'yi devre dışı bırakır `GetValues2` :</span><span class="sxs-lookup"><span data-stu-id="fcc74-198">The following code disables CORS for the `GetValues2` action:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

<span data-ttu-id="fcc74-199">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="fcc74-199">The preceding code:</span></span>

* <span data-ttu-id="fcc74-200">, [Uç nokta yönlendirme](#ecors)ile CORS 'yi etkinleştirmez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-200">Doesn't enable CORS with [endpoint routing](#ecors).</span></span>
* <span data-ttu-id="fcc74-201">[Varsayılan BIR CORS ilkesi](#dp)tanımlamaz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-201">Doesn't define a [default CORS policy](#dp).</span></span>
* <span data-ttu-id="fcc74-202">Denetleyicinin CORS ilkesini etkinleştirmek için [[Enablecors ("MyPolicy")]](#attr) kullanır `"MyPolicy"` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-202">Uses [[EnableCors("MyPolicy")]](#attr) to enable the `"MyPolicy"` CORS policy for the controller.</span></span>
* <span data-ttu-id="fcc74-203">Yöntemi için CORS 'yi devre dışı bırakır `GetValues2` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-203">Disables CORS for the `GetValues2` method.</span></span>

<span data-ttu-id="fcc74-204">Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Test CORS](#testc) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-204">See [Test CORS](#testc) for instructions on testing the preceding code.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="fcc74-205">CORS ilke seçenekleri</span><span class="sxs-lookup"><span data-stu-id="fcc74-205">CORS policy options</span></span>

<span data-ttu-id="fcc74-206">Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-206">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="fcc74-207">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="fcc74-207">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="fcc74-208">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-208">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="fcc74-209">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-209">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="fcc74-210">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-210">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="fcc74-211">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="fcc74-211">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="fcc74-212">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-212">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="fcc74-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-213"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fcc74-214">Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-214">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="fcc74-215">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="fcc74-215">Set the allowed origins</span></span>

<span data-ttu-id="fcc74-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-216"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="fcc74-217">`AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-217">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc74-218">`AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-218">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="fcc74-219">Bir uygulama her iki yöntemle yapılandırıldığında, CORS hizmeti geçersiz bir CORS yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="fcc74-219">The CORS service returns an invalid CORS response when an app is configured with both methods.</span></span>

<span data-ttu-id="fcc74-220">`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-220">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="fcc74-221">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="fcc74-221">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="fcc74-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-222"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="fcc74-223">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-223">Set the allowed HTTP methods</span></span>

<span data-ttu-id="fcc74-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="fcc74-224"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="fcc74-225">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-225">Allows any HTTP method:</span></span>
* <span data-ttu-id="fcc74-226">Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-226">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="fcc74-227">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="fcc74-227">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="fcc74-228">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-228">Set the allowed request headers</span></span>

<span data-ttu-id="fcc74-229">Belirli başlıkların, [Yazar isteği üstbilgileri](https://xhr.spec.whatwg.org/#request)ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:</span><span class="sxs-lookup"><span data-stu-id="fcc74-229">To allow specific headers to be sent in a CORS request, called [author request headers](https://xhr.spec.whatwg.org/#request), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="fcc74-230">Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-230">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="fcc74-231">`AllowAnyHeader`ön kontrol isteklerini ve [erişim-denetim-istek-üst](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) bilgi üst bilgisini etkiler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-231">`AllowAnyHeader` affects preflight requests and the [Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method) header.</span></span> <span data-ttu-id="fcc74-232">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="fcc74-232">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="fcc74-233">Tarafından belirtilen belirli başlıklarıyla eşleşen bir CORS ara yazılım ilkesi, `WithHeaders` yalnızca `Access-Control-Request-Headers` ' de belirtilen üstbilgiler ile tam olarak eşleşiyorsa mümkündür `WithHeaders` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-233">A CORS Middleware policy match to specific headers specified by `WithHeaders` is only possible when the headers sent in `Access-Control-Request-Headers` exactly match the headers stated in `WithHeaders`.</span></span>

<span data-ttu-id="fcc74-234">Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="fcc74-234">For instance, consider an app configured as follows:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

<span data-ttu-id="fcc74-235">CORS ara yazılımı, `Content-Language` ([headernames. contentlanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) içinde listelenmediği için aşağıdaki istek üst bilgisi ile bir ön denetim isteğini reddettiğinde `WithHeaders` :</span><span class="sxs-lookup"><span data-stu-id="fcc74-235">CORS Middleware declines a preflight request with the following request header because `Content-Language` ([HeaderNames.ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)) isn't listed in `WithHeaders`:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

<span data-ttu-id="fcc74-236">Uygulama *200 ok* yanıtı DÖNDÜRÜYOR ancak CORS üst bilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-236">The app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="fcc74-237">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-237">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="fcc74-238">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-238">Set the exposed response headers</span></span>

<span data-ttu-id="fcc74-239">Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-239">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="fcc74-240">Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="fcc74-240">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="fcc74-241">Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-241">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="fcc74-242">CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-242">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="fcc74-243">Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-243">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="fcc74-244">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="fcc74-244">Credentials in cross-origin requests</span></span>

<span data-ttu-id="fcc74-245">Kimlik bilgileri CORS isteğinde özel işleme gerektirir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-245">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="fcc74-246">Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-246">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="fcc74-247">Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-247">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="fcc74-248">Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-248">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="fcc74-249">`XMLHttpRequest`Doğrudan kullanarak:</span><span class="sxs-lookup"><span data-stu-id="fcc74-249">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="fcc74-250">JQuery kullanarak:</span><span class="sxs-lookup"><span data-stu-id="fcc74-250">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="fcc74-251">[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:</span><span class="sxs-lookup"><span data-stu-id="fcc74-251">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="fcc74-252">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-252">The server must allow the credentials.</span></span> <span data-ttu-id="fcc74-253">Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-253">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

<span data-ttu-id="fcc74-254">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-254">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="fcc74-255">Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-255">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="fcc74-256">Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-256">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="fcc74-257">Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-257">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="fcc74-258">CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-258">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

<a name="pref"></a>

## <a name="preflight-requests"></a><span data-ttu-id="fcc74-259">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="fcc74-259">Preflight requests</span></span>

<span data-ttu-id="fcc74-260">Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir [seçenek](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-260">For some CORS requests, the browser sends an additional [OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) request before making the actual request.</span></span> <span data-ttu-id="fcc74-261">Bu isteğe bir [ön kontrol isteği](https://developer.mozilla.org/docs/Glossary/Preflight_request)denir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-261">This request is called a [preflight request](https://developer.mozilla.org/docs/Glossary/Preflight_request).</span></span> <span data-ttu-id="fcc74-262">Aşağıdaki koşulların **Tümü** doğruysa tarayıcı, ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-262">The browser can skip the preflight request if **all** the following conditions are true:</span></span>

* <span data-ttu-id="fcc74-263">İstek yöntemi al, HEAD veya POST.</span><span class="sxs-lookup"><span data-stu-id="fcc74-263">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="fcc74-264">Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-264">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="fcc74-265">, `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="fcc74-265">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="fcc74-266">İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-266">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="fcc74-267">CORS belirtimi, bu üst bilgiler [Yazar istek üst bilgilerini](https://www.w3.org/TR/cors/#author-request-headers)çağırır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-267">The CORS specification calls these headers [author request headers](https://www.w3.org/TR/cors/#author-request-headers).</span></span> <span data-ttu-id="fcc74-268">Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-268">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="fcc74-269">Aşağıda, bu belgenin [Test CORS](#testc) bölümündeki **[testi koy]** düğmesinden yapılmış olan ön kontrol isteğine benzer bir yanıt verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-269">The following is an example response similar to the preflight request made from the **[Put test]** button in the [Test CORS](#testc) section of this document.</span></span>

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

<span data-ttu-id="fcc74-270">Ön kontrol isteği [http seçenekleri](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-270">The preflight request uses the [HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS) method.</span></span> <span data-ttu-id="fcc74-271">Aşağıdaki üstbilgiler bulunabilir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-271">It may include the following headers:</span></span>

* <span data-ttu-id="fcc74-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): gerçek istek IÇIN kullanılacak http yöntemi.</span><span class="sxs-lookup"><span data-stu-id="fcc74-272">[Access-Control-Request-Method](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method): The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="fcc74-273">[Erişim-denetim-istek-üstbilgiler](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi.</span><span class="sxs-lookup"><span data-stu-id="fcc74-273">[Access-Control-Request-Headers](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers): A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="fcc74-274">Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-274">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>
* [<span data-ttu-id="fcc74-275">Erişim-denetim-Izin-Yöntemler</span><span class="sxs-lookup"><span data-stu-id="fcc74-275">Access-Control-Allow-Methods</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

<span data-ttu-id="fcc74-276">Ön kontrol isteği reddedilirse, uygulama bir `200 OK` yanıt döndürür ancak CORS üst bilgilerini yapmaz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-276">If the preflight request is denied, the app returns a `200 OK` response but doesn't set the CORS headers.</span></span> <span data-ttu-id="fcc74-277">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-277">Therefore, the browser doesn't attempt the cross-origin request.</span></span> <span data-ttu-id="fcc74-278">Reddedilen bir ön kontrol isteğine bir örnek için, bu belgenin [Test CORS](#testc) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-278">For an example of a denied preflight request, see the [Test CORS](#testc) section of this document.</span></span>

<span data-ttu-id="fcc74-279">Konsol uygulaması, F12 araçlarını kullanarak tarayıcıya bağlı olarak aşağıdakilerden birine benzer bir hata gösterir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-279">Using the F12 tools, the console app shows an error similar to one of the following, depending on the browser:</span></span>

* <span data-ttu-id="fcc74-280">Firefox: çapraz kaynak Isteği engellendi: aynı kaynak Ilkesi, konumundaki uzak kaynağı okumaktan izin vermez `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-280">Firefox: Cross-Origin Request Blocked: The Same Origin Policy disallows reading the remote resource at `https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`.</span></span> <span data-ttu-id="fcc74-281">(Neden: CORS isteği başarılı olmadı).</span><span class="sxs-lookup"><span data-stu-id="fcc74-281">(Reason: CORS request did not succeed).</span></span> [<span data-ttu-id="fcc74-282">Daha Fazla Bilgi Edinin</span><span class="sxs-lookup"><span data-stu-id="fcc74-282">Learn More</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* <span data-ttu-id="fcc74-283">Kmıum tabanlı: ' ' kaynağından ' ' konumundaki getirme erişimi https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5 https://cors3.azurewebsites.net CORS ilkesi tarafından engellendi: ön kontrol Isteğine verilen yanıt erişim denetimi denetimine geçti: İstenen kaynakta ' Access-Control-Allow-Origin ' üst bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="fcc74-283">Chromium based: Access to fetch at 'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5' from origin 'https://cors3.azurewebsites.net' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="fcc74-284">Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-284">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>

<span data-ttu-id="fcc74-285">Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-285">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

<span data-ttu-id="fcc74-286">Tüm [Yazar isteği başlıklarına](https://www.w3.org/TR/cors/#author-request-headers)izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-286">To allow all [author request headers](https://www.w3.org/TR/cors/#author-request-headers), call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

<span data-ttu-id="fcc74-287">Tarayıcılar, nasıl ayarlandıklarından tutarlı değildir `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-287">Browsers aren't consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="fcc74-288">Aşağıdakilerden biri:</span><span class="sxs-lookup"><span data-stu-id="fcc74-288">If either:</span></span>

* <span data-ttu-id="fcc74-289">Üst bilgiler, dışında bir şeye ayarlanır`"*"`</span><span class="sxs-lookup"><span data-stu-id="fcc74-289">Headers are set to anything other than `"*"`</span></span>
* <span data-ttu-id="fcc74-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>çağrıldı: en az `Accept` , `Content-Type` , ve, ve `Origin` desteklemek istediğiniz tüm özel üstbilgileri ekleyin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-290"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> is called: Include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a><span data-ttu-id="fcc74-291">Otomatik ön kontrol istek kodu</span><span class="sxs-lookup"><span data-stu-id="fcc74-291">Automatic preflight request code</span></span>

<span data-ttu-id="fcc74-292">CORS ilkesi uygulandığında:</span><span class="sxs-lookup"><span data-stu-id="fcc74-292">When the CORS policy is applied either:</span></span>

* <span data-ttu-id="fcc74-293">' İ çağırarak küresel olarak `app.UseCors` `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-293">Globally by calling `app.UseCors` in `Startup.Configure`.</span></span>
* <span data-ttu-id="fcc74-294">Özniteliği kullanılıyor `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-294">Using the `[EnableCors]` attribute.</span></span>

<span data-ttu-id="fcc74-295">ASP.NET Core, ön kontrol SEÇENEKLERI isteğine yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-295">ASP.NET Core responds to the preflight OPTIONS request.</span></span>

<span data-ttu-id="fcc74-296">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` Otomatik ön kontrol **not** istekleri desteklenmez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-296">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support automatic preflight requests.</span></span>

<span data-ttu-id="fcc74-297">Bu belgenin [Test CORS](#testc) bölümü bu davranışı gösterir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-297">The [Test CORS](#testc) section of this document demonstrates this behavior.</span></span>

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a><span data-ttu-id="fcc74-298">[HttpOptions] ön kontrol istekleri için öznitelik</span><span class="sxs-lookup"><span data-stu-id="fcc74-298">[HttpOptions] attribute for preflight requests</span></span>

<span data-ttu-id="fcc74-299">CORS uygun ilkeyle etkinleştirildiğinde, ASP.NET Core genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-299">When CORS is enabled with the appropriate policy, ASP.NET Core generally responds to CORS preflight requests automatically.</span></span> <span data-ttu-id="fcc74-300">Bazı senaryolarda bu durum olmayabilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-300">In some scenarios, this may not be the case.</span></span> <span data-ttu-id="fcc74-301">Örneğin, [uç nokta yönlendirme Ile CORS](#ecors)'yi kullanma.</span><span class="sxs-lookup"><span data-stu-id="fcc74-301">For example, using [CORS with endpoint routing](#ecors).</span></span>

<span data-ttu-id="fcc74-302">Aşağıdaki kod, Seçenekler istekleri için uç noktalar oluşturmak için [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) özniteliğini kullanır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-302">The following code uses the [[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute) attribute to create endpoints for OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

<span data-ttu-id="fcc74-303">Önceki kodun test edilmesine ilişkin yönergeler için bkz. [Endpoint Routing Ile test CORS ve [HttpOptions]](#tcer) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-303">See [Test CORS with endpoint routing and [HttpOptions]](#tcer) for instructions on testing the preceding code.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="fcc74-304">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-304">Set the preflight expiration time</span></span>

<span data-ttu-id="fcc74-305">`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-305">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="fcc74-306">Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-306">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="fcc74-307">CORS nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="fcc74-307">How CORS works</span></span>

<span data-ttu-id="fcc74-308">Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-308">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="fcc74-309">CORS bir güvenlik özelliği **değil** .</span><span class="sxs-lookup"><span data-stu-id="fcc74-309">CORS is **not** a security feature.</span></span> <span data-ttu-id="fcc74-310">CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-310">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="fcc74-311">Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyası (XSS)](xref:security/cross-site-scripting) kullanabilir ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-311">For example, a malicious actor could use [Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="fcc74-312">Bir API, CORS 'ye izin vererek daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-312">An API isn't safer by allowing CORS.</span></span>
  * <span data-ttu-id="fcc74-313">CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-313">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="fcc74-314">Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-314">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="fcc74-315">Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-315">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="fcc74-316">Fiddler</span><span class="sxs-lookup"><span data-stu-id="fcc74-316">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="fcc74-317">Postman</span><span class="sxs-lookup"><span data-stu-id="fcc74-317">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="fcc74-318">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="fcc74-318">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="fcc74-319">Adres çubuğuna URL girerek bir Web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="fcc74-319">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="fcc74-320">Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-320">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="fcc74-321">CORS içermeyen tarayıcılar, çıkış noktaları arası istekleri yapamıyor.</span><span class="sxs-lookup"><span data-stu-id="fcc74-321">Browsers without CORS can't do cross-origin requests.</span></span> <span data-ttu-id="fcc74-322">CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-322">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="fcc74-323">JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-323">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="fcc74-324">Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-324">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="fcc74-325">[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-325">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="fcc74-326">Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-326">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="fcc74-327">CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-327">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="fcc74-328">Dağıtılan [örnekteki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [testi Yerleştir düğmesi](https://cors3.azurewebsites.net/test)</span><span class="sxs-lookup"><span data-stu-id="fcc74-328">The  [PUT test button](https://cors3.azurewebsites.net/test) on the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)</span></span>

<span data-ttu-id="fcc74-329">Aşağıda, [Values](https://cors3.azurewebsites.net/) test düğmesinden olan bir çapraz kaynak isteğine bir örnek verilmiştir `https://cors1.azurewebsites.net/api/values` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-329">The following is an example of a cross-origin request from the [Values](https://cors3.azurewebsites.net/) test button to `https://cors1.azurewebsites.net/api/values`.</span></span> <span data-ttu-id="fcc74-330">`Origin`Üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="fcc74-330">The `Origin` header:</span></span>

* <span data-ttu-id="fcc74-331">, İsteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-331">Provides the domain of the site that's making the request.</span></span>
* <span data-ttu-id="fcc74-332">Gereklidir ve konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-332">Is required and must be different from the host.</span></span>

<span data-ttu-id="fcc74-333">**Genel üstbilgiler**</span><span class="sxs-lookup"><span data-stu-id="fcc74-333">**General headers**</span></span>

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

<span data-ttu-id="fcc74-334">**Yanıt üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="fcc74-334">**Response headers**</span></span>

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

<span data-ttu-id="fcc74-335">**İstek üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="fcc74-335">**Request headers**</span></span>

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

<span data-ttu-id="fcc74-336">`OPTIONS`İsteklerde, sunucu yanıtta **yanıt üstbilgileri** `Access-Control-Allow-Origin: {allowed origin}` üst bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-336">In `OPTIONS` requests, the server sets the **Response headers** `Access-Control-Allow-Origin: {allowed origin}` header in the response.</span></span> <span data-ttu-id="fcc74-337">Örneğin, dağıtılan [örnek](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [delete [enablecors]](https://cors1.azurewebsites.net/test?number=2) düğme `OPTIONS` isteği aşağıdaki üstbilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-337">For example, the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI), [Delete [EnableCors]](https://cors1.azurewebsites.net/test?number=2) button `OPTIONS` request contains the following  headers:</span></span>

<span data-ttu-id="fcc74-338">**Genel üstbilgiler**</span><span class="sxs-lookup"><span data-stu-id="fcc74-338">**General headers**</span></span>

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

<span data-ttu-id="fcc74-339">**Yanıt üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="fcc74-339">**Response headers**</span></span>

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

<span data-ttu-id="fcc74-340">**İstek üst bilgileri**</span><span class="sxs-lookup"><span data-stu-id="fcc74-340">**Request headers**</span></span>

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

<span data-ttu-id="fcc74-341">Önceki **yanıt üst bilgilerinde**sunucu, yanıtta [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) üst bilgisini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-341">In the preceding **Response headers**, the server sets the [Access-Control-Allow-Origin](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin) header in the response.</span></span> <span data-ttu-id="fcc74-342">`https://cors1.azurewebsites.net`Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-342">The `https://cors1.azurewebsites.net` value of this header matches the `Origin` header from the request.</span></span>

<span data-ttu-id="fcc74-343"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>Çağrılırsa, `Access-Control-Allow-Origin: *` joker karakter değeri döndürülür.</span><span class="sxs-lookup"><span data-stu-id="fcc74-343">If <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> is called, the `Access-Control-Allow-Origin: *`, the wildcard value, is returned.</span></span> <span data-ttu-id="fcc74-344">`AllowAnyOrigin`Tüm kaynağa izin verir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-344">`AllowAnyOrigin` allows any origin.</span></span>

<span data-ttu-id="fcc74-345">Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-345">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="fcc74-346">Özellikle, tarayıcı isteğe izin vermez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-346">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="fcc74-347">Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-347">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="options"></a>

### <a name="display-options-requests"></a><span data-ttu-id="fcc74-348">Görüntüleme SEÇENEKLERI istekleri</span><span class="sxs-lookup"><span data-stu-id="fcc74-348">Display OPTIONS requests</span></span>

<span data-ttu-id="fcc74-349">Varsayılan olarak, Chrome ve Edge tarayıcıları, F12 araçlarının Ağ sekmesinde seçenek isteklerini göstermez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-349">By default, the Chrome and Edge browsers don't show OPTIONS requests on the network tab of the F12 tools.</span></span> <span data-ttu-id="fcc74-350">Bu tarayıcılarda seçenek isteklerini göstermek için:</span><span class="sxs-lookup"><span data-stu-id="fcc74-350">To display OPTIONS requests in these browsers:</span></span>

* <span data-ttu-id="fcc74-351">`chrome://flags/#out-of-blink-cors` veya `edge://flags/#out-of-blink-cors`</span><span class="sxs-lookup"><span data-stu-id="fcc74-351">`chrome://flags/#out-of-blink-cors` or `edge://flags/#out-of-blink-cors`</span></span>
* <span data-ttu-id="fcc74-352">bayrağı devre dışı bırakın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-352">disable the flag.</span></span>
* <span data-ttu-id="fcc74-353">uygulamasını.</span><span class="sxs-lookup"><span data-stu-id="fcc74-353">restart.</span></span>

<span data-ttu-id="fcc74-354">Firefox, varsayılan olarak seçenek isteklerini gösterir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-354">Firefox shows OPTIONS requests by default.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="fcc74-355">IIS 'de CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-355">CORS in IIS</span></span>

<span data-ttu-id="fcc74-356">IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-356">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="fcc74-357">Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-357">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

<a name="testc"></a>

## <a name="test-cors"></a><span data-ttu-id="fcc74-358">Test CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-358">Test CORS</span></span>

<span data-ttu-id="fcc74-359">[Örnek indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) , CORS 'yi test etmek için kod içerir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-359">The [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) has code to test CORS.</span></span> <span data-ttu-id="fcc74-360">Bkz. [indirme](xref:index#how-to-download-a-sample).</span><span class="sxs-lookup"><span data-stu-id="fcc74-360">See [how to download](xref:index#how-to-download-a-sample).</span></span> <span data-ttu-id="fcc74-361">Örnek, eklenen sayfaların bulunduğu bir API projem örneğidir Razor :</span><span class="sxs-lookup"><span data-stu-id="fcc74-361">The sample is an API project with Razor Pages added:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > <span data-ttu-id="fcc74-362">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-362">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors).</span></span>

<span data-ttu-id="fcc74-363">Aşağıdakiler, `ValuesController` test için uç noktaları sağlar:</span><span class="sxs-lookup"><span data-stu-id="fcc74-363">The following `ValuesController` provides the endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

<span data-ttu-id="fcc74-364">[Mydisplayrouteınfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) , [Rick. docs. Samples. routeınfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet paketi tarafından sağlanır ve rota bilgilerini görüntüler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-364">[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs) is provided by the [Rick.Docs.Samples.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet package and displays route information.</span></span>

<span data-ttu-id="fcc74-365">Aşağıdaki yaklaşımlardan birini kullanarak önceki örnek kodu test edin:</span><span class="sxs-lookup"><span data-stu-id="fcc74-365">Test the preceding sample code by using one of the following approaches:</span></span>

* <span data-ttu-id="fcc74-366">Üzerinde dağıtılan örnek uygulamayı kullanın [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-366">Use the deployed sample app at [https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/).</span></span> <span data-ttu-id="fcc74-367">Örneği indirmeniz gerekmez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-367">There is no need to download the sample.</span></span>
* <span data-ttu-id="fcc74-368">`dotnet run`Varsayılan URL 'sini kullanarak örneği çalıştırın `https://localhost:5001` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-368">Run the sample with `dotnet run` using the default URL of `https://localhost:5001`.</span></span>
* <span data-ttu-id="fcc74-369">Bir URL 'SI için, bağlantı noktası 44398 olarak ayarlanan Visual Studio 'daki örneği çalıştırın `https://localhost:44398` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-369">Run the sample from Visual Studio with the port set to 44398 for a URL of `https://localhost:44398`.</span></span>

<span data-ttu-id="fcc74-370">F12 araçlarıyla bir tarayıcı kullanma:</span><span class="sxs-lookup"><span data-stu-id="fcc74-370">Using a browser with the F12 tools:</span></span>

* <span data-ttu-id="fcc74-371">**Değerler** düğmesini seçin ve **ağ** sekmesindeki üst bilgileri gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-371">Select the **Values** button and review the headers in the **Network** tab.</span></span>
* <span data-ttu-id="fcc74-372">**Testi yerleştir** düğmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-372">Select the **PUT test** button.</span></span> <span data-ttu-id="fcc74-373">Seçenekler isteği görüntüleme yönergeleri için bkz. [görüntüleme seçenekleri istekleri](#options) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-373">See [Display OPTIONS requests](#options) for instructions on displaying the OPTIONS request.</span></span> <span data-ttu-id="fcc74-374">**PUT testi** iki istek, bir seçenekler ön hazırlığı ISTEğI ve PUT isteği oluşturur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-374">The **PUT test** creates two requests, an OPTIONS preflight request and the PUT request.</span></span>
* <span data-ttu-id="fcc74-375">**`GetValues2 [DisableCors]`** Başarısız BIR CORS isteğini tetiklemek için düğmeyi seçin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-375">Select the **`GetValues2 [DisableCors]`** button to trigger a failed CORS request.</span></span> <span data-ttu-id="fcc74-376">Belgede bahsedildiği gibi, yanıt 200 başarılı döndürür, ancak CORS isteği yapılmaz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-376">As mentioned in the document, the response returns 200 success, but the CORS request is not made.</span></span> <span data-ttu-id="fcc74-377">CORS hatasını görmek için **konsol** sekmesini seçin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-377">Select the **Console** tab to see the CORS error.</span></span> <span data-ttu-id="fcc74-378">Tarayıcıya bağlı olarak aşağıdakine benzer bir hata görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-378">Depending on the browser, an error similar to the following is displayed:</span></span>

     <span data-ttu-id="fcc74-379">`'https://cors1.azurewebsites.net/api/values/GetValues2'`Kaynak kimden kaynağı `'https://cors3.azurewebsites.net'` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok.</span><span class="sxs-lookup"><span data-stu-id="fcc74-379">Access to fetch at `'https://cors1.azurewebsites.net/api/values/GetValues2'` from origin `'https://cors3.azurewebsites.net'` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.</span></span> <span data-ttu-id="fcc74-380">Donuk bir yanıt ihtiyaçlarınıza hizmet veriyorsa, isteği CORS devre dışı olarak getirmek için isteğin modunu ' No-CORS ' olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-380">If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.</span></span>
     
<span data-ttu-id="fcc74-381">CORS özellikli uç noktalar, [kıvrımlı](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler)veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-381">CORS-enabled endpoints can be tested with a tool, such as [curl](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler), or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="fcc74-382">Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-382">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="fcc74-383">İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :</span><span class="sxs-lookup"><span data-stu-id="fcc74-383">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="fcc74-384">CORS ara yazılımı için isteği işleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-384">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="fcc74-385">CORS üstbilgileri yanıtta döndürülmedi.</span><span class="sxs-lookup"><span data-stu-id="fcc74-385">CORS headers aren't returned in the response.</span></span>

<span data-ttu-id="fcc74-386">Aşağıdaki komut, `curl` bilgileri içeren BIR seçenek isteği vermek için kullanır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-386">The following command uses `curl` to issue an OPTIONS request with information:</span></span>

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a><span data-ttu-id="fcc74-387">ENDPOINT yönlendirme ve [HttpOptions] ile test CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-387">Test CORS with endpoint routing and [HttpOptions]</span></span>

<span data-ttu-id="fcc74-388">Şu anda bir uç nokta temelinde CORS etkinleştirildiğinde `RequireCors` [Otomatik ön kontrol istekleri](#apf) **desteklenmez** .</span><span class="sxs-lookup"><span data-stu-id="fcc74-388">Enabling CORS on a per-endpoint basis using `RequireCors` currently does **not** support [automatic preflight requests](#apf).</span></span> <span data-ttu-id="fcc74-389">[CORS 'yi etkinleştirmek için Endpoint Routing](#ecors)kullanan aşağıdaki kodu göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="fcc74-389">Consider the following code which uses [endpoint routing to enable CORS](#ecors):</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

<span data-ttu-id="fcc74-390">Aşağıdakiler `TodoItems1Controller` test için uç noktalar sağlar:</span><span class="sxs-lookup"><span data-stu-id="fcc74-390">The following `TodoItems1Controller` provides endpoints for testing:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

<span data-ttu-id="fcc74-391">Önceki kodu dağıtılan [Örneğin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [Test sayfasından](https://cors1.azurewebsites.net/test?number=1) test edin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-391">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=1) of the deployed [sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI).</span></span>

<span data-ttu-id="fcc74-392">Bitiş noktaları, ön kontrol isteklerini içerdiğinden ve yanıt verdiği için **delete [enablecors]** ve **Get [enablecors]** düğmeleri başarılı olur `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-392">The **Delete [EnableCors]** and **GET [EnableCors]** buttons succeed, because the endpoints have `[EnableCors]` and respond to preflight requests.</span></span> <span data-ttu-id="fcc74-393">Diğer uç noktalar başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-393">The other endpoints fails.</span></span> <span data-ttu-id="fcc74-394">[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) şu şekilde gönderdiği için **Al** düğmesi başarısız olur:</span><span class="sxs-lookup"><span data-stu-id="fcc74-394">The **GET** button fails, because the [JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js) sends:</span></span>

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

<span data-ttu-id="fcc74-395">Aşağıdakiler `TodoItems2Controller` benzer uç noktalar sağlar, ancak seçenek isteklerine yanıt vermek için açık kod içerir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-395">The following `TodoItems2Controller` provides similar endpoints, but includes explicit code to respond to OPTIONS requests:</span></span>

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

<span data-ttu-id="fcc74-396">Önceki kodu dağıtılan örneğin [Test sayfasından](https://cors1.azurewebsites.net/test?number=2) test edin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-396">Test the preceding code from the [test page](https://cors1.azurewebsites.net/test?number=2) of the deployed sample.</span></span> <span data-ttu-id="fcc74-397">**Denetleyici** açılan listesinde, **ön kontrol** ' yı ve ardından **denetleyiciyi ayarla**' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-397">In the **Controller** drop down list, select **Preflight** and then **Set Controller**.</span></span> <span data-ttu-id="fcc74-398">Uç noktalara yönelik tüm CORS çağrıları `TodoItems2Controller` başarılı olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-398">All the CORS calls to the `TodoItems2Controller` endpoints succeed.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fcc74-399">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fcc74-399">Additional resources</span></span>

* [<span data-ttu-id="fcc74-400">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="fcc74-400">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="fcc74-401">IIS CORS modülünü kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-401">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fcc74-402">Gönderen [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fcc74-402">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="fcc74-403">Bu makalede, ASP.NET Core uygulamasında CORS 'nin nasıl etkinleştirileceği gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-403">This article shows how to enable CORS in an ASP.NET Core app.</span></span>

<span data-ttu-id="fcc74-404">Tarayıcı güvenliği, bir Web sayfasının Web sayfasını sunduğundan farklı bir etki alanına istek yapmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="fcc74-404">Browser security prevents a web page from making requests to a different domain than the one that served the web page.</span></span> <span data-ttu-id="fcc74-405">Bu kısıtlamaya *aynı-Origin ilkesi*adı verilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-405">This restriction is called the *same-origin policy*.</span></span> <span data-ttu-id="fcc74-406">Aynı-kaynak ilkesi, kötü niyetli bir sitenin gizli verileri başka bir siteden okumasını engeller.</span><span class="sxs-lookup"><span data-stu-id="fcc74-406">The same-origin policy prevents a malicious site from reading sensitive data from another site.</span></span> <span data-ttu-id="fcc74-407">Bazen diğer sitelerin uygulamanıza çapraz çıkış istekleri yapmasına izin vermek isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-407">Sometimes, you might want to allow other sites make cross-origin requests to your app.</span></span> <span data-ttu-id="fcc74-408">Daha fazla bilgi için bkz. [MOZILLA CORS makalesi](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span><span class="sxs-lookup"><span data-stu-id="fcc74-408">For more information, see the [Mozilla CORS article](https://developer.mozilla.org/docs/Web/HTTP/CORS).</span></span>

<span data-ttu-id="fcc74-409">[Çapraz kaynak kaynak paylaşımı](https://www.w3.org/TR/cors/) (CORS):</span><span class="sxs-lookup"><span data-stu-id="fcc74-409">[Cross Origin Resource Sharing](https://www.w3.org/TR/cors/) (CORS):</span></span>

* <span data-ttu-id="fcc74-410">, Bir sunucunun aynı kaynak ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-410">Is a W3C standard that allows a server to relax the same-origin policy.</span></span>
* <span data-ttu-id="fcc74-411">Güvenlik özelliği **değil** , CORS güvenliği.</span><span class="sxs-lookup"><span data-stu-id="fcc74-411">Is **not** a security feature, CORS relaxes security.</span></span> <span data-ttu-id="fcc74-412">CORS 'nin izin vermesini sağlayan bir API daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-412">An API is not safer by allowing CORS.</span></span> <span data-ttu-id="fcc74-413">Daha fazla bilgi için bkz. [CORS çalışma](#how-cors).</span><span class="sxs-lookup"><span data-stu-id="fcc74-413">For more information, see [How CORS works](#how-cors).</span></span>
* <span data-ttu-id="fcc74-414">Bir sunucunun bazı çapraz kaynak isteklerine, diğerlerini reddetirken açık olarak izin almasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-414">Allows a server to explicitly allow some cross-origin requests while rejecting others.</span></span>
* <span data-ttu-id="fcc74-415">, [JSONP](/dotnet/framework/wcf/samples/jsonp)gibi önceki tekniklerin daha güvenli ve daha esnektir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-415">Is safer and more flexible than earlier techniques, such as [JSONP](/dotnet/framework/wcf/samples/jsonp).</span></span>

<span data-ttu-id="fcc74-416">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fcc74-416">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="same-origin"></a><span data-ttu-id="fcc74-417">Aynı kaynak</span><span class="sxs-lookup"><span data-stu-id="fcc74-417">Same origin</span></span>

<span data-ttu-id="fcc74-418">Özdeş şemaları, konakları ve bağlantı noktalarına sahip olmaları durumunda iki URL aynı kaynağa sahiptir ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span><span class="sxs-lookup"><span data-stu-id="fcc74-418">Two URLs have the same origin if they have identical schemes, hosts, and ports ([RFC 6454](https://tools.ietf.org/html/rfc6454)).</span></span>

<span data-ttu-id="fcc74-419">Bu iki URL aynı kaynağa sahiptir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-419">These two URLs have the same origin:</span></span>

* `https://example.com/foo.html`
* `https://example.com/bar.html`

<span data-ttu-id="fcc74-420">Bu URL 'Ler, önceki iki URL 'den farklı kaynaklardan farklıdır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-420">These URLs have different origins than the previous two URLs:</span></span>

* <span data-ttu-id="fcc74-421">`https://example.net`: Farklı etki alanı</span><span class="sxs-lookup"><span data-stu-id="fcc74-421">`https://example.net`: Different domain</span></span>
* <span data-ttu-id="fcc74-422">`https://www.example.com/foo.html`: Farklı alt etki alanı</span><span class="sxs-lookup"><span data-stu-id="fcc74-422">`https://www.example.com/foo.html`: Different subdomain</span></span>
* <span data-ttu-id="fcc74-423">`http://example.com/foo.html`: Farklı düzen</span><span class="sxs-lookup"><span data-stu-id="fcc74-423">`http://example.com/foo.html`: Different scheme</span></span>
* <span data-ttu-id="fcc74-424">`https://example.com:9000/foo.html`: Farklı bağlantı noktası</span><span class="sxs-lookup"><span data-stu-id="fcc74-424">`https://example.com:9000/foo.html`: Different port</span></span>

<span data-ttu-id="fcc74-425">Internet Explorer, kaynakları karşılaştırırken bağlantı noktasını dikkate almaz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-425">Internet Explorer doesn't consider the port when comparing origins.</span></span>

## <a name="cors-with-named-policy-and-middleware"></a><span data-ttu-id="fcc74-426">Adlandırılmış ilke ve ara yazılım ile CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-426">CORS with named policy and middleware</span></span>

<span data-ttu-id="fcc74-427">CORS ara yazılımı, çıkış noktaları arası istekleri işler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-427">CORS Middleware handles cross-origin requests.</span></span> <span data-ttu-id="fcc74-428">Aşağıdaki kod, belirtilen kaynağa sahip tüm uygulama için CORS 'yi sunar:</span><span class="sxs-lookup"><span data-stu-id="fcc74-428">The following code enables CORS for the entire app with the specified origin:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

<span data-ttu-id="fcc74-429">Yukarıdaki kod:</span><span class="sxs-lookup"><span data-stu-id="fcc74-429">The preceding code:</span></span>

* <span data-ttu-id="fcc74-430">İlke adını " \_ myallowspecifickaynaklarına" olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-430">Sets the policy name to "\_myAllowSpecificOrigins".</span></span> <span data-ttu-id="fcc74-431">İlke adı rastgele olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-431">The policy name is arbitrary.</span></span>
* <span data-ttu-id="fcc74-432"><xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>CORS 'yi sağlayan genişletme yöntemini çağırır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-432">Calls the <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> extension method, which enables CORS.</span></span>
* <span data-ttu-id="fcc74-433"><xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [Lambda ifadesi](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)ile çağrılar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-433">Calls <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> with a [lambda expression](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions).</span></span> <span data-ttu-id="fcc74-434">Lambda bir nesnesi alır <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> .</span><span class="sxs-lookup"><span data-stu-id="fcc74-434">The lambda takes a <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> object.</span></span> <span data-ttu-id="fcc74-435">Gibi [yapılandırma seçenekleri](#cors-policy-options), `WithOrigins` Bu makalenin ilerleyen kısımlarında açıklanmıştır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-435">[Configuration options](#cors-policy-options), such as `WithOrigins`, are described later in this article.</span></span>

<span data-ttu-id="fcc74-436"><xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>Yöntem çağrısı, uygulamanın hizmet KAPSAYıCıSıNA CORS Hizmetleri ekler:</span><span class="sxs-lookup"><span data-stu-id="fcc74-436">The <xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> method call adds CORS services to the app's service container:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

<span data-ttu-id="fcc74-437">Daha fazla bilgi için bu belgedeki [CORS ilke seçenekleri](#cpo) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-437">For more information, see [CORS policy options](#cpo) in this document .</span></span>

<span data-ttu-id="fcc74-438"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>Yöntemi aşağıdaki kodda gösterildiği gibi yöntemleri zincirleyebilir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-438">The <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> method can chain methods, as shown in the following code:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

<span data-ttu-id="fcc74-439">Not: URL sonunda eğik **not** çizgi ( `/` ) bulunmamalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-439">Note: The URL must **not** contain a trailing slash (`/`).</span></span> <span data-ttu-id="fcc74-440">URL ile sonlandığında `/` karşılaştırma döndürülür `false` ve üst bilgi döndürülmez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-440">If the URL terminates with `/`, the comparison returns `false` and no header is returned.</span></span>

<span data-ttu-id="fcc74-441">Aşağıdaki kod, CORS ara yazılımı aracılığıyla CORS ilkelerini tüm uygulama uç noktalarına uygular:</span><span class="sxs-lookup"><span data-stu-id="fcc74-441">The following code applies CORS policies to all the apps endpoints via CORS Middleware:</span></span>
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
<span data-ttu-id="fcc74-442">Note: öğesinden `UseCors` önce çağrılmalıdır `UseMvc` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-442">Note: `UseCors` must be called before `UseMvc`.</span></span>

<span data-ttu-id="fcc74-443">CORS ilkesini sayfa/denetleyici/eylem düzeyinde uygulamak için bkz. [ Razor sayfalarda, denetleyicilerde ve eylem yöntemlerinde CORS 'yi etkinleştirme](#ecors) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-443">See [Enable CORS in Razor Pages, controllers, and action methods](#ecors) to apply CORS policy at the page/controller/action level.</span></span>

<span data-ttu-id="fcc74-444">Yukarıdaki koda benzer test kodu hakkında yönergeler için bkz. [Test CORS](#test) .</span><span class="sxs-lookup"><span data-stu-id="fcc74-444">See [Test CORS](#test) for instructions on testing code similar to the preceding code.</span></span>

## <a name="enable-cors-with-attributes"></a><span data-ttu-id="fcc74-445">CORS 'yi özniteliklerle etkinleştir</span><span class="sxs-lookup"><span data-stu-id="fcc74-445">Enable CORS with attributes</span></span>

<span data-ttu-id="fcc74-446">[ &lbrack; Enablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) özniteliği, CORS 'yi küresel olarak uygulamaya bir alternatif sağlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-446">The [&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute) attribute provides an alternative to applying CORS globally.</span></span> <span data-ttu-id="fcc74-447">`[EnableCors]`Öznitelik, tüm bitiş noktaları yerine, seçilen bitiş noktaları IÇIN CORS 'yi mümkün.</span><span class="sxs-lookup"><span data-stu-id="fcc74-447">The `[EnableCors]` attribute enables CORS for selected end points, rather than all end points.</span></span>

<span data-ttu-id="fcc74-448">`[EnableCors]`Varsayılan ilkeyi belirtmek ve `[EnableCors("{Policy String}")]` bir ilke belirlemek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-448">Use `[EnableCors]` to specify the default policy and `[EnableCors("{Policy String}")]` to specify a policy.</span></span>

<span data-ttu-id="fcc74-449">`[EnableCors]`Özniteliği öğesine uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-449">The `[EnableCors]` attribute can be applied to:</span></span>

* Razor<span data-ttu-id="fcc74-450">Sayfasında`PageModel`</span><span class="sxs-lookup"><span data-stu-id="fcc74-450"> Page `PageModel`</span></span>
* <span data-ttu-id="fcc74-451">Kumandasını</span><span class="sxs-lookup"><span data-stu-id="fcc74-451">Controller</span></span>
* <span data-ttu-id="fcc74-452">Denetleyici eylemi yöntemi</span><span class="sxs-lookup"><span data-stu-id="fcc74-452">Controller action method</span></span>

<span data-ttu-id="fcc74-453">Özniteliği ile denetleyici/sayfa-model/eylem 'e farklı ilkeler uygulayabilirsiniz `[EnableCors]` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-453">You can apply different policies to controller/page-model/action with the  `[EnableCors]` attribute.</span></span> <span data-ttu-id="fcc74-454">`[EnableCors]`Özniteliği bir denetleyiciler/sayfa modeli/eylem yöntemine uygulandığında ve bu işlem, ara yazılım 'NDA CORS 'yi etkinleştirmişse, **her iki ilke de** uygulanır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-454">When the `[EnableCors]` attribute is applied to a controllers/page model/action method, and CORS is enabled in middleware, **both** policies are applied.</span></span> <span data-ttu-id="fcc74-455">İlkeleri **birleştirmediğimiz** için önerilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-455">We recommend **not** combining policies.</span></span> <span data-ttu-id="fcc74-456">`[EnableCors]`Özniteliği veya ara yazılımı kullanın, **her ikisini birden**kullanmayın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-456">Use the `[EnableCors]` attribute or middleware, **not both**.</span></span> <span data-ttu-id="fcc74-457">Kullanırken `[EnableCors]` , varsayılan bir **not** ilke tanımlamayın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-457">When using `[EnableCors]`, do **not** define a default policy.</span></span>

<span data-ttu-id="fcc74-458">Aşağıdaki kod her bir yönteme farklı bir ilke uygular:</span><span class="sxs-lookup"><span data-stu-id="fcc74-458">The following code applies a different policy to each method:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

<span data-ttu-id="fcc74-459">Aşağıdaki kod, bir CORS varsayılan ilkesi ve adlı bir ilke oluşturur `"AnotherPolicy"` :</span><span class="sxs-lookup"><span data-stu-id="fcc74-459">The following code creates a CORS default policy and a policy named `"AnotherPolicy"`:</span></span>

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a><span data-ttu-id="fcc74-460">CORS 'yi devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="fcc74-460">Disable CORS</span></span>

<span data-ttu-id="fcc74-461">[ &lbrack; Disablecors &rbrack; ](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) özniteliği denetleyici/sayfa MODELI/eylem için CORS 'yi devre dışı bırakır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-461">The [&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute) attribute disables CORS for the controller/page-model/action.</span></span>

<a name="cpo"></a>

## <a name="cors-policy-options"></a><span data-ttu-id="fcc74-462">CORS ilke seçenekleri</span><span class="sxs-lookup"><span data-stu-id="fcc74-462">CORS policy options</span></span>

<span data-ttu-id="fcc74-463">Bu bölümde, bir CORS ilkesinde ayarlanmakta olabilecek çeşitli seçenekler açıklanmaktadır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-463">This section describes the various options that can be set in a CORS policy:</span></span>

* [<span data-ttu-id="fcc74-464">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="fcc74-464">Set the allowed origins</span></span>](#set-the-allowed-origins)
* [<span data-ttu-id="fcc74-465">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-465">Set the allowed HTTP methods</span></span>](#set-the-allowed-http-methods)
* [<span data-ttu-id="fcc74-466">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-466">Set the allowed request headers</span></span>](#set-the-allowed-request-headers)
* [<span data-ttu-id="fcc74-467">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-467">Set the exposed response headers</span></span>](#set-the-exposed-response-headers)
* [<span data-ttu-id="fcc74-468">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="fcc74-468">Credentials in cross-origin requests</span></span>](#credentials-in-cross-origin-requests)
* [<span data-ttu-id="fcc74-469">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-469">Set the preflight expiration time</span></span>](#set-the-preflight-expiration-time)

<span data-ttu-id="fcc74-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>içinde çağırılır `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-470"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> is called in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fcc74-471">Bazı seçenekler için, ilk olarak [CORS 'Nin nasıl çalıştığı](#how-cors) bölümü okumanız yararlı olabilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-471">For some options, it may be helpful to read the [How CORS works](#how-cors) section first.</span></span>

## <a name="set-the-allowed-origins"></a><span data-ttu-id="fcc74-472">İzin verilen kaynakları ayarla</span><span class="sxs-lookup"><span data-stu-id="fcc74-472">Set the allowed origins</span></span>

<span data-ttu-id="fcc74-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Herhangi bir düzen (veya) ile tüm kaynaklardan gelen CORS isteklerine izin verir `http` `https` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-473"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>: Allows CORS requests from all origins with any scheme (`http` or `https`).</span></span> <span data-ttu-id="fcc74-474">`AllowAnyOrigin`, *herhangi bir Web sitesi* uygulamaya çapraz kaynak istekleri yapabildiğinden güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-474">`AllowAnyOrigin` is insecure because *any website* can make cross-origin requests to the app.</span></span>

> [!NOTE]
> <span data-ttu-id="fcc74-475">`AllowAnyOrigin`Ve `AllowCredentials` güvenli olmayan bir yapılandırma belirtip, siteler arası istek sahteciliği ile sonuçlanabilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-475">Specifying `AllowAnyOrigin` and `AllowCredentials` is an insecure configuration and can result in cross-site request forgery.</span></span> <span data-ttu-id="fcc74-476">Güvenli bir uygulama için, istemcinin sunucu kaynaklarına erişim yetkisi olması gerekiyorsa, kaynakların tam bir listesini belirtin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-476">For a secure app, specify an exact list of origins if the client must authorize itself to access server resources.</span></span>

<span data-ttu-id="fcc74-477">`AllowAnyOrigin`ön kontrol isteklerini ve `Access-Control-Allow-Origin` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-477">`AllowAnyOrigin` affects preflight requests and the `Access-Control-Allow-Origin` header.</span></span> <span data-ttu-id="fcc74-478">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="fcc74-478">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="fcc74-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> Kaynağa izin verilip verilmediğini değerlendirirken, kaynağın yapılandırılmış bir joker karakterle eşleşmesini sağlayan bir işlev olarak ilkenin özelliğini ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-479"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>: Sets the <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> property of the policy to be a function that allows origins to match a configured wildcard domain when evaluating if the origin is allowed.</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a><span data-ttu-id="fcc74-480">İzin verilen HTTP yöntemlerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-480">Set the allowed HTTP methods</span></span>

<span data-ttu-id="fcc74-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span><span class="sxs-lookup"><span data-stu-id="fcc74-481"><xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:</span></span>

* <span data-ttu-id="fcc74-482">Herhangi bir HTTP yöntemine izin verir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-482">Allows any HTTP method:</span></span>
* <span data-ttu-id="fcc74-483">Ön kontrol isteklerini ve `Access-Control-Allow-Methods` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-483">Affects preflight requests and the `Access-Control-Allow-Methods` header.</span></span> <span data-ttu-id="fcc74-484">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="fcc74-484">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

### <a name="set-the-allowed-request-headers"></a><span data-ttu-id="fcc74-485">İzin verilen istek üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-485">Set the allowed request headers</span></span>

<span data-ttu-id="fcc74-486">Belirli başlıkların, *Yazar isteği üstbilgileri*ADLı bir CORS isteğinde gönderilmesine izin vermek için, ' i çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> ve izin verilen üst bilgileri belirtin:</span><span class="sxs-lookup"><span data-stu-id="fcc74-486">To allow specific headers to be sent in a CORS request, called *author request headers*, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> and specify the allowed headers:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="fcc74-487">Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-487">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="fcc74-488">Bu ayar, ön kontrol isteklerini ve `Access-Control-Request-Headers` üstbilgiyi etkiler.</span><span class="sxs-lookup"><span data-stu-id="fcc74-488">This setting affects preflight requests and the `Access-Control-Request-Headers` header.</span></span> <span data-ttu-id="fcc74-489">Daha fazla bilgi için bkz. [ön kontrol istekleri](#preflight-requests) bölümü.</span><span class="sxs-lookup"><span data-stu-id="fcc74-489">For more information, see the [Preflight requests](#preflight-requests) section.</span></span>

<span data-ttu-id="fcc74-490">CORS ara yazılımı her zaman `Access-Control-Request-Headers` CorsPolicy. Headers ' de yapılandırılan değerlerden bağımsız olarak, ' deki dört üst bilgilerin gönderilmesine izin verir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-490">CORS Middleware always allows four headers in the `Access-Control-Request-Headers` to be sent regardless of the values configured in CorsPolicy.Headers.</span></span> <span data-ttu-id="fcc74-491">Bu üst bilgi listesi şunları içerir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-491">This list of headers includes:</span></span>

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

<span data-ttu-id="fcc74-492">Örneğin, aşağıdaki gibi yapılandırılmış bir uygulamayı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="fcc74-492">For instance, consider an app configured as follows:</span></span>

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

<span data-ttu-id="fcc74-493">CORS ara yazılımı, `Content-Language` her zaman beyaz listelenmiş olduğundan, aşağıdaki istek üstbilgisiyle bir ön kontrol isteğine başarıyla yanıt veriyor:</span><span class="sxs-lookup"><span data-stu-id="fcc74-493">CORS Middleware responds successfully to a preflight request with the following request header because `Content-Language` is always whitelisted:</span></span>

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a><span data-ttu-id="fcc74-494">Gösterilen yanıt üst bilgilerini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-494">Set the exposed response headers</span></span>

<span data-ttu-id="fcc74-495">Varsayılan olarak tarayıcı, tüm yanıt üst bilgilerini uygulamaya sunmaz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-495">By default, the browser doesn't expose all of the response headers to the app.</span></span> <span data-ttu-id="fcc74-496">Daha fazla bilgi için bkz. [W3C çıkış noktaları arası kaynak paylaşımı (terminoloji): basit yanıt üst bilgisi](https://www.w3.org/TR/cors/#simple-response-header).</span><span class="sxs-lookup"><span data-stu-id="fcc74-496">For more information, see [W3C Cross-Origin Resource Sharing (Terminology): Simple Response Header](https://www.w3.org/TR/cors/#simple-response-header).</span></span>

<span data-ttu-id="fcc74-497">Varsayılan olarak kullanılabilen yanıt üstbilgileri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="fcc74-497">The response headers that are available by default are:</span></span>

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

<span data-ttu-id="fcc74-498">CORS belirtimi, bu üst bilgiler *basit yanıt üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-498">The CORS specification calls these headers *simple response headers*.</span></span> <span data-ttu-id="fcc74-499">Diğer üst bilgileri uygulama için kullanılabilir hale getirmek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-499">To make other headers available to the app, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a><span data-ttu-id="fcc74-500">Kaynaklar arası isteklerde kimlik bilgileri</span><span class="sxs-lookup"><span data-stu-id="fcc74-500">Credentials in cross-origin requests</span></span>

<span data-ttu-id="fcc74-501">Kimlik bilgileri CORS isteğinde özel işleme gerektirir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-501">Credentials require special handling in a CORS request.</span></span> <span data-ttu-id="fcc74-502">Varsayılan olarak tarayıcı, kimlik bilgilerini bir çapraz kaynak isteğiyle göndermez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-502">By default, the browser doesn't send credentials with a cross-origin request.</span></span> <span data-ttu-id="fcc74-503">Kimlik bilgileri, tanımlama bilgileri ve HTTP kimlik doğrulama düzenlerini içerir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-503">Credentials include cookies and HTTP authentication schemes.</span></span> <span data-ttu-id="fcc74-504">Bir çapraz kaynak isteğiyle kimlik bilgilerini göndermek için, istemcisinin olarak ayarlanması gerekir `XMLHttpRequest.withCredentials` `true` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-504">To send credentials with a cross-origin request, the client must set `XMLHttpRequest.withCredentials` to `true`.</span></span>

<span data-ttu-id="fcc74-505">`XMLHttpRequest`Doğrudan kullanarak:</span><span class="sxs-lookup"><span data-stu-id="fcc74-505">Using `XMLHttpRequest` directly:</span></span>

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

<span data-ttu-id="fcc74-506">JQuery kullanarak:</span><span class="sxs-lookup"><span data-stu-id="fcc74-506">Using jQuery:</span></span>

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

<span data-ttu-id="fcc74-507">[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)'sini kullanma:</span><span class="sxs-lookup"><span data-stu-id="fcc74-507">Using the [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API):</span></span>

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

<span data-ttu-id="fcc74-508">Sunucu kimlik bilgilerine izin vermelidir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-508">The server must allow the credentials.</span></span> <span data-ttu-id="fcc74-509">Çıkış noktaları arası kimlik bilgilerine izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-509">To allow cross-origin credentials, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

<span data-ttu-id="fcc74-510">HTTP yanıtı, `Access-Control-Allow-Credentials` tarayıcıya sunucunun bir çapraz kaynak isteği için kimlik bilgileri verdiğini bildiren bir üst bilgi içerir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-510">The HTTP response includes an `Access-Control-Allow-Credentials` header, which tells the browser that the server allows credentials for a cross-origin request.</span></span>

<span data-ttu-id="fcc74-511">Tarayıcı kimlik bilgilerini gönderirse ancak yanıt geçerli bir `Access-Control-Allow-Credentials` üst bilgi içermiyorsa, tarayıcı uygulamaya yanıtı kullanıma sunmaz ve çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-511">If the browser sends credentials but the response doesn't include a valid `Access-Control-Allow-Credentials` header, the browser doesn't expose the response to the app, and the cross-origin request fails.</span></span>

<span data-ttu-id="fcc74-512">Çıkış noktaları arası kimlik bilgilerine izin vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-512">Allowing cross-origin credentials is a security risk.</span></span> <span data-ttu-id="fcc74-513">Başka bir etki alanındaki Web sitesi, kullanıcının bilgisi olmadan kullanıcı adına, oturum açmış bir kullanıcının kimlik bilgilerini uygulamaya gönderebilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-513">A website at another domain can send a signed-in user's credentials to the app on the user's behalf without the user's knowledge.</span></span> <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

<span data-ttu-id="fcc74-514">CORS belirtimi Ayrıca `"*"` üst bilgi varsa, çıkış (tüm kaynaklar) ayarının geçersiz olduğunu belirtir `Access-Control-Allow-Credentials` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-514">The CORS specification also states that setting origins to `"*"` (all origins) is invalid if the `Access-Control-Allow-Credentials` header is present.</span></span>

### <a name="preflight-requests"></a><span data-ttu-id="fcc74-515">Ön kontrol istekleri</span><span class="sxs-lookup"><span data-stu-id="fcc74-515">Preflight requests</span></span>

<span data-ttu-id="fcc74-516">Bazı CORS istekleri için, tarayıcı gerçek isteği yapmadan önce ek bir istek gönderir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-516">For some CORS requests, the browser sends an additional request before making the actual request.</span></span> <span data-ttu-id="fcc74-517">Bu isteğe bir *ön kontrol isteği*denir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-517">This request is called a *preflight request*.</span></span> <span data-ttu-id="fcc74-518">Aşağıdaki koşullar doğruysa tarayıcı, ön kontrol isteğini atlayabilir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-518">The browser can skip the preflight request if the following conditions are true:</span></span>

* <span data-ttu-id="fcc74-519">İstek yöntemi al, HEAD veya POST.</span><span class="sxs-lookup"><span data-stu-id="fcc74-519">The request method is GET, HEAD, or POST.</span></span>
* <span data-ttu-id="fcc74-520">Uygulama,,, veya dışındaki istek üst bilgilerini ayarlanmamış `Accept` `Accept-Language` `Content-Language` `Content-Type` `Last-Event-ID` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-520">The app doesn't set request headers other than `Accept`, `Accept-Language`, `Content-Language`, `Content-Type`, or `Last-Event-ID`.</span></span>
* <span data-ttu-id="fcc74-521">, `Content-Type` Ayarlandıysa, aşağıdaki değerlerden birine sahip olan üst bilgi:</span><span class="sxs-lookup"><span data-stu-id="fcc74-521">The `Content-Type` header, if set, has one of the following values:</span></span>
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

<span data-ttu-id="fcc74-522">İstemci isteği için ayarlanan istek üst bilgileri kuralı, uygulamanın, nesne üzerinde çağırarak ayarladığı üst bilgiler için geçerlidir `setRequestHeader` `XMLHttpRequest` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-522">The rule on request headers set for the client request applies to headers that the app sets by calling `setRequestHeader` on the `XMLHttpRequest` object.</span></span> <span data-ttu-id="fcc74-523">CORS belirtimi, bu üst bilgiler *Yazar istek üst bilgilerini*çağırır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-523">The CORS specification calls these headers *author request headers*.</span></span> <span data-ttu-id="fcc74-524">Kural,, veya gibi tarayıcının ayarlayabilmesi için, veya gibi bir üst bilgiye uygulanmaz `User-Agent` `Host` `Content-Length` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-524">The rule doesn't apply to headers the browser can set, such as `User-Agent`, `Host`, or `Content-Length`.</span></span>

<span data-ttu-id="fcc74-525">Aşağıda bir ön denetim isteğine örnek verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-525">The following is an example of a preflight request:</span></span>

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

<span data-ttu-id="fcc74-526">Uçuş öncesi isteği HTTP SEÇENEKLERI yöntemini kullanır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-526">The pre-flight request uses the HTTP OPTIONS method.</span></span> <span data-ttu-id="fcc74-527">İki özel üst bilgi içerir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-527">It includes two special headers:</span></span>

* <span data-ttu-id="fcc74-528">`Access-Control-Request-Method`: Gerçek istek için kullanılacak HTTP yöntemi.</span><span class="sxs-lookup"><span data-stu-id="fcc74-528">`Access-Control-Request-Method`: The HTTP method that will be used for the actual request.</span></span>
* <span data-ttu-id="fcc74-529">`Access-Control-Request-Headers`: Uygulamanın gerçek istekte ayarladığı istek üst bilgilerinin bir listesi.</span><span class="sxs-lookup"><span data-stu-id="fcc74-529">`Access-Control-Request-Headers`: A list of request headers that the app sets on the actual request.</span></span> <span data-ttu-id="fcc74-530">Daha önce belirtildiği gibi, bu, tarayıcının ayarladığı üst bilgileri içermez `User-Agent` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-530">As stated earlier, this doesn't include headers that the browser sets, such as `User-Agent`.</span></span>

<!-- I think this needs to be removed, was put here accidently -->

<span data-ttu-id="fcc74-531">CORS uygun ilkeyle etkinleştirildiğinde ASP.NET Core, genellikle CORS ön denetim isteklerini otomatik olarak yanıtlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-531">When CORS is enabled with the appropriate policy, ASP.NET Core generally automatically responds to CORS preflight requests.</span></span> <span data-ttu-id="fcc74-532">[Ön kontrol istekleri için bkz. [HttpOptions] özniteliği](#pro).</span><span class="sxs-lookup"><span data-stu-id="fcc74-532">See [[HttpOptions] attribute for preflight requests](#pro).</span></span>

<span data-ttu-id="fcc74-533">CORS ön kontrol isteği `Access-Control-Request-Headers` , sunucuya gerçek istekle gönderilen üstbilgileri belirten bir üst bilgi içerebilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-533">A CORS preflight request might include an `Access-Control-Request-Headers` header, which indicates to the server the headers that are sent with the actual request.</span></span>

<span data-ttu-id="fcc74-534">Belirli üstbilgilere izin vermek için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-534">To allow specific headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

<span data-ttu-id="fcc74-535">Tüm yazar isteği başlıklarına izin vermek için şunu çağırın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-535">To allow all author request headers, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

<span data-ttu-id="fcc74-536">Tarayıcılar, nasıl ayarlandıklarından tamamen tutarlı değildir `Access-Control-Request-Headers` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-536">Browsers aren't entirely consistent in how they set `Access-Control-Request-Headers`.</span></span> <span data-ttu-id="fcc74-537">Üst bilgileri `"*"` (veya kullanımı) dışında bir şeye ayarlarsanız, <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*> desteklemek istediğiniz en az `Accept` , `Content-Type` , ve ve `Origin` Ek üstbilgileri dahil etmelisiniz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-537">If you set headers to anything other than `"*"` (or use <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>), you should include at least `Accept`, `Content-Type`, and `Origin`, plus any custom headers that you want to support.</span></span>

<span data-ttu-id="fcc74-538">Aşağıda, ön kontrol isteğine örnek bir yanıt verilmiştir (sunucunun isteğe izin verdiği varsayıldığında):</span><span class="sxs-lookup"><span data-stu-id="fcc74-538">The following is an example response to the preflight request (assuming that the server allows the request):</span></span>

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

<span data-ttu-id="fcc74-539">Yanıt, izin verilen `Access-Control-Allow-Methods` üst bilgileri listeleyen, izin verilen yöntemleri ve isteğe bağlı olarak bir üst bilgiyi listeleyen bir üst bilgi içerir `Access-Control-Allow-Headers` .</span><span class="sxs-lookup"><span data-stu-id="fcc74-539">The response includes an `Access-Control-Allow-Methods` header that lists the allowed methods and optionally an `Access-Control-Allow-Headers` header, which lists the allowed headers.</span></span> <span data-ttu-id="fcc74-540">Ön kontrol isteği başarılı olursa, tarayıcı gerçek isteği gönderir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-540">If the preflight request succeeds, the browser sends the actual request.</span></span>

<span data-ttu-id="fcc74-541">Ön kontrol isteği reddedilirse, uygulama *200 ok* yanıtı döndürür ancak CORS üst bilgilerini geri göndermez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-541">If the preflight request is denied, the app returns a *200 OK* response but doesn't send the CORS headers back.</span></span> <span data-ttu-id="fcc74-542">Bu nedenle tarayıcı, çıkış noktaları arası isteği denemez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-542">Therefore, the browser doesn't attempt the cross-origin request.</span></span>

### <a name="set-the-preflight-expiration-time"></a><span data-ttu-id="fcc74-543">Ön kontrol sona erme süresini ayarlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-543">Set the preflight expiration time</span></span>

<span data-ttu-id="fcc74-544">`Access-Control-Max-Age`Üst bilgi, ön kontrol isteğine olan yanıtın ne kadar süreyle önbelleğe alınacağını belirtir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-544">The `Access-Control-Max-Age` header specifies how long the response to the preflight request can be cached.</span></span> <span data-ttu-id="fcc74-545">Bu üstbilgiyi ayarlamak için şunu arayın <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*> :</span><span class="sxs-lookup"><span data-stu-id="fcc74-545">To set this header, call <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>:</span></span>

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a><span data-ttu-id="fcc74-546">CORS nasıl kullanılır?</span><span class="sxs-lookup"><span data-stu-id="fcc74-546">How CORS works</span></span>

<span data-ttu-id="fcc74-547">Bu bölümde, HTTP iletileri düzeyindeki bir [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) isteğinde ne olacağı açıklanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-547">This section describes what happens in a [CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS) request at the level of the HTTP messages.</span></span>

* <span data-ttu-id="fcc74-548">CORS bir güvenlik özelliği **değil** .</span><span class="sxs-lookup"><span data-stu-id="fcc74-548">CORS is **not** a security feature.</span></span> <span data-ttu-id="fcc74-549">CORS, bir sunucunun aynı kaynaklı ilkeyi rahat bir şekilde sağlamasına izin veren bir W3C standardıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-549">CORS is a W3C standard that allows a server to relax the same-origin policy.</span></span>
  * <span data-ttu-id="fcc74-550">Örneğin, kötü niyetli bir aktör sitenize karşı [siteler arası komut dosyalarını (XSS) engelliyor](xref:security/cross-site-scripting) ve bilgileri çalmak için CORS özellikli sitesine bir siteler arası istek yürütebilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-550">For example, a malicious actor could use [Prevent Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) against your site and execute a cross-site request to their CORS enabled site to steal information.</span></span>
* <span data-ttu-id="fcc74-551">CORS 'nin izin vermesini sağlayan API 'niz daha güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-551">Your API is not safer by allowing CORS.</span></span>
  * <span data-ttu-id="fcc74-552">CORS 'yi zorlamak için istemciye (tarayıcı) sahiptir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-552">It's up to the client (browser) to enforce CORS.</span></span> <span data-ttu-id="fcc74-553">Sunucu isteği yürütür ve yanıtı döndürür. Bu, bir hatayı döndüren ve yanıtı engelleyen istemcdir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-553">The server executes the request and returns the response, it's the client that returns an error and blocks the response.</span></span> <span data-ttu-id="fcc74-554">Örneğin, aşağıdaki araçlardan herhangi birinde sunucu yanıtı görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="fcc74-554">For example, any of the following tools will display the server response:</span></span>
    * [<span data-ttu-id="fcc74-555">Fiddler</span><span class="sxs-lookup"><span data-stu-id="fcc74-555">Fiddler</span></span>](https://www.telerik.com/fiddler)
    * [<span data-ttu-id="fcc74-556">Postman</span><span class="sxs-lookup"><span data-stu-id="fcc74-556">Postman</span></span>](https://www.getpostman.com/)
    * [<span data-ttu-id="fcc74-557">.NET HttpClient</span><span class="sxs-lookup"><span data-stu-id="fcc74-557">.NET HttpClient</span></span>](/dotnet/csharp/tutorials/console-webapiclient)
    * <span data-ttu-id="fcc74-558">Adres çubuğuna URL girerek bir Web tarayıcısı.</span><span class="sxs-lookup"><span data-stu-id="fcc74-558">A web browser by entering the URL in the address bar.</span></span>
* <span data-ttu-id="fcc74-559">Bir sunucu, tarayıcıların çapraz kaynak [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) veya [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) isteği çalıştırmasına izin vermesinin yasaktır bir yoludur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-559">It's a way for a server to allow browsers to execute a cross-origin [XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) or [Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API) request that otherwise would be forbidden.</span></span>
  * <span data-ttu-id="fcc74-560">Tarayıcılar (CORS olmadan), çapraz kaynak istekleri yapamıyor.</span><span class="sxs-lookup"><span data-stu-id="fcc74-560">Browsers (without CORS) can't do cross-origin requests.</span></span> <span data-ttu-id="fcc74-561">CORS 'den önce, bu kısıtlamayı aşmak için [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-561">Before CORS, [JSONP](https://www.w3schools.com/js/js_json_jsonp.asp) was used to circumvent this restriction.</span></span> <span data-ttu-id="fcc74-562">JSONP XHR kullanmaz, `<script>` yanıtı almak için etiketini kullanır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-562">JSONP doesn't use XHR, it uses the `<script>` tag to receive the response.</span></span> <span data-ttu-id="fcc74-563">Betiklerin, çapraz kaynak olarak yüklenmesine izin verilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-563">Scripts are allowed to be loaded cross-origin.</span></span>

<span data-ttu-id="fcc74-564">[CORS belirtimi](https://www.w3.org/TR/cors/) , çıkış noktaları arası istekleri etkinleştiren bırkaç yeni http üst bilgisi sunmuştur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-564">The [CORS specification](https://www.w3.org/TR/cors/) introduced several new HTTP headers that enable cross-origin requests.</span></span> <span data-ttu-id="fcc74-565">Bir tarayıcı CORS 'yi destekliyorsa, bu üst bilgileri, çıkış noktaları arası istekler için otomatik olarak ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-565">If a browser supports CORS, it sets these headers automatically for cross-origin requests.</span></span> <span data-ttu-id="fcc74-566">CORS 'yi etkinleştirmek için özel JavaScript kodu gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-566">Custom JavaScript code isn't required to enable CORS.</span></span>

<span data-ttu-id="fcc74-567">Aşağıda, bir çapraz kaynak isteğine bir örnek verilmiştir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-567">The following is an example of a cross-origin request.</span></span> <span data-ttu-id="fcc74-568">`Origin`Üst bilgi, isteği yapan sitenin etki alanını sağlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-568">The `Origin` header provides the domain of the site that's making the request.</span></span> <span data-ttu-id="fcc74-569">`Origin`Üst bilgi gereklidir ve konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-569">The `Origin` header is required and must be different from the host.</span></span>

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

<span data-ttu-id="fcc74-570">Sunucu isteğe izin veriyorsa, `Access-Control-Allow-Origin` yanıttaki üstbilgiyi ayarlar.</span><span class="sxs-lookup"><span data-stu-id="fcc74-570">If the server allows the request, it sets the `Access-Control-Allow-Origin` header in the response.</span></span> <span data-ttu-id="fcc74-571">Bu üstbilginin değeri, `Origin` istekten gelen üstbilgiyle eşleşir veya joker değerdir `"*"` , yani herhangi bir kaynağa izin verilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-571">The value of this header either matches the `Origin` header from the request or is the wildcard value `"*"`, meaning that any origin is allowed:</span></span>

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

<span data-ttu-id="fcc74-572">Yanıt `Access-Control-Allow-Origin` üstbilgiyi içermiyorsa, çapraz kaynak isteği başarısız olur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-572">If the response doesn't include the `Access-Control-Allow-Origin` header, the cross-origin request fails.</span></span> <span data-ttu-id="fcc74-573">Özellikle, tarayıcı isteğe izin vermez.</span><span class="sxs-lookup"><span data-stu-id="fcc74-573">Specifically, the browser disallows the request.</span></span> <span data-ttu-id="fcc74-574">Sunucu başarılı bir yanıt döndürse bile tarayıcı, yanıtı istemci uygulama için kullanılabilir hale getirir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-574">Even if the server returns a successful response, the browser doesn't make the response available to the client app.</span></span>

<a name="test"></a>

## <a name="test-cors"></a><span data-ttu-id="fcc74-575">Test CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-575">Test CORS</span></span>

<span data-ttu-id="fcc74-576">CORS 'yi sınamak için:</span><span class="sxs-lookup"><span data-stu-id="fcc74-576">To test CORS:</span></span>

1. <span data-ttu-id="fcc74-577">[BIR API projesi oluşturun](xref:tutorials/first-web-api).</span><span class="sxs-lookup"><span data-stu-id="fcc74-577">[Create an API project](xref:tutorials/first-web-api).</span></span> <span data-ttu-id="fcc74-578">Alternatif olarak, [örneği de indirebilirsiniz](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span><span class="sxs-lookup"><span data-stu-id="fcc74-578">Alternatively, you can [download the sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors).</span></span>
1. <span data-ttu-id="fcc74-579">Bu belgedeki yaklaşımlardan birini kullanarak CORS 'yi etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-579">Enable CORS using one of the approaches in this document.</span></span> <span data-ttu-id="fcc74-580">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="fcc74-580">For example:</span></span>

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > <span data-ttu-id="fcc74-581">`WithOrigins("https://localhost:<port>");`yalnızca [indirme örnek koduna](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)benzer bir örnek uygulamanın test edilmesi için kullanılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-581">`WithOrigins("https://localhost:<port>");` should only be used for testing a sample app similar to the [download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors).</span></span>

1. <span data-ttu-id="fcc74-582">Web uygulaması projesi ( Razor Sayfalar veya Mvc) oluşturun.</span><span class="sxs-lookup"><span data-stu-id="fcc74-582">Create a web app project (Razor Pages or MVC).</span></span> <span data-ttu-id="fcc74-583">Örnek, Razor sayfaları kullanır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-583">The sample uses Razor Pages.</span></span> <span data-ttu-id="fcc74-584">Web uygulamasını, API projesiyle aynı çözümde oluşturabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="fcc74-584">You can create the web app in the same solution as the API project.</span></span>
1. <span data-ttu-id="fcc74-585">Aşağıdaki Vurgulanan kodu *Index. cshtml* dosyasına ekleyin:</span><span class="sxs-lookup"><span data-stu-id="fcc74-585">Add the following highlighted code to the *Index.cshtml* file:</span></span>

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. <span data-ttu-id="fcc74-586">Yukarıdaki kodda, `url: 'https://<web app>.azurewebsites.net/api/values/1',` dağıtılan UYGULAMANıN URL 'siyle değiştirin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-586">In the preceding code, replace `url: 'https://<web app>.azurewebsites.net/api/values/1',` with the URL to the deployed app.</span></span>
1. <span data-ttu-id="fcc74-587">API projesini dağıtın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-587">Deploy the API project.</span></span> <span data-ttu-id="fcc74-588">Örneğin, [Azure 'a dağıtın](xref:host-and-deploy/azure-apps/index).</span><span class="sxs-lookup"><span data-stu-id="fcc74-588">For example, [deploy to Azure](xref:host-and-deploy/azure-apps/index).</span></span>
1. <span data-ttu-id="fcc74-589">RazorMasaüstünden sayfaları veya MVC uygulamasını çalıştırın ve **Test** düğmesine tıklayın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-589">Run the Razor Pages or MVC app from the desktop and click on the **Test** button.</span></span> <span data-ttu-id="fcc74-590">Hata iletilerini gözden geçirmek için F12 araçlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-590">Use the F12 tools to review error messages.</span></span>
1. <span data-ttu-id="fcc74-591">Localhost kaynağını kaldırın `WithOrigins` ve uygulamayı dağıtın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-591">Remove the localhost origin from `WithOrigins` and deploy the app.</span></span> <span data-ttu-id="fcc74-592">Alternatif olarak, istemci uygulamasını farklı bir bağlantı noktasıyla çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-592">Alternatively, run the client app with a different port.</span></span> <span data-ttu-id="fcc74-593">Örneğin, Visual Studio 'dan çalıştırın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-593">For example, run from Visual Studio.</span></span>
1. <span data-ttu-id="fcc74-594">İstemci uygulamasıyla test edin.</span><span class="sxs-lookup"><span data-stu-id="fcc74-594">Test with the client app.</span></span> <span data-ttu-id="fcc74-595">CORS hataları bir hata döndürüyor, ancak hata iletisi JavaScript için kullanılabilir değil.</span><span class="sxs-lookup"><span data-stu-id="fcc74-595">CORS failures return an error, but the error message isn't available to JavaScript.</span></span> <span data-ttu-id="fcc74-596">Hatayı görmek için F12 araçlarındaki konsol sekmesini kullanın.</span><span class="sxs-lookup"><span data-stu-id="fcc74-596">Use the console tab in the F12 tools to see the error.</span></span> <span data-ttu-id="fcc74-597">Tarayıcıya bağlı olarak, aşağıdakine benzer bir hata alırsınız (F12 araçları konsolunda):</span><span class="sxs-lookup"><span data-stu-id="fcc74-597">Depending on the browser, you get an error (in the F12 tools console) similar to the following:</span></span>

   * <span data-ttu-id="fcc74-598">Microsoft Edge 'i kullanarak:</span><span class="sxs-lookup"><span data-stu-id="fcc74-598">Using Microsoft Edge:</span></span>

     <span data-ttu-id="fcc74-599">**SEC7120: [CORS] kaynak, `https://localhost:44375` `https://localhost:44375` ' deki çıkış noktaları için erişim-denetim-Izin-kaynak yanıt üstbilgisinde bulunamadı`https://webapi.azurewebsites.net/api/values/1`**</span><span class="sxs-lookup"><span data-stu-id="fcc74-599">**SEC7120: [CORS] The origin `https://localhost:44375` did not find `https://localhost:44375` in the Access-Control-Allow-Origin response header for cross-origin  resource at `https://webapi.azurewebsites.net/api/values/1`**</span></span>

   * <span data-ttu-id="fcc74-600">Chrome kullanarak:</span><span class="sxs-lookup"><span data-stu-id="fcc74-600">Using Chrome:</span></span>

     <span data-ttu-id="fcc74-601">**Kaynak başında XMLHttpRequest erişimi `https://webapi.azurewebsites.net/api/values/1` `https://localhost:44375` CORS ilkesi tarafından engellendi: İstenen kaynakta ' erişim-denetim-Izin-Origin ' üst bilgisi yok.**</span><span class="sxs-lookup"><span data-stu-id="fcc74-601">**Access to XMLHttpRequest at `https://webapi.azurewebsites.net/api/values/1` from origin `https://localhost:44375` has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.**</span></span>
     
<span data-ttu-id="fcc74-602">CORS özellikli uç noktalar [Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/)gibi bir araçla test edilebilir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-602">CORS-enabled endpoints can be tested with a tool, such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/).</span></span> <span data-ttu-id="fcc74-603">Bir araç kullanırken, üst bilgi tarafından belirtilen isteğin kaynağı `Origin` isteği alan konaktan farklı olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-603">When using a tool, the origin of the request specified by the `Origin` header must differ from the host receiving the request.</span></span> <span data-ttu-id="fcc74-604">İstek, üst bilgi değerine göre *Çıkış dışı* değilse `Origin` :</span><span class="sxs-lookup"><span data-stu-id="fcc74-604">If the request isn't *cross-origin* based on the value of the `Origin` header:</span></span>

* <span data-ttu-id="fcc74-605">CORS ara yazılımı için isteği işleme gereksinimi yoktur.</span><span class="sxs-lookup"><span data-stu-id="fcc74-605">There's no need for CORS Middleware to process the request.</span></span>
* <span data-ttu-id="fcc74-606">CORS üstbilgileri yanıtta döndürülmedi.</span><span class="sxs-lookup"><span data-stu-id="fcc74-606">CORS headers aren't returned in the response.</span></span>

## <a name="cors-in-iis"></a><span data-ttu-id="fcc74-607">IIS 'de CORS</span><span class="sxs-lookup"><span data-stu-id="fcc74-607">CORS in IIS</span></span>

<span data-ttu-id="fcc74-608">IIS 'ye dağıtım yaparken, sunucu anonim erişime izin verecek şekilde yapılandırılmamışsa CORS, Windows kimlik doğrulamasından önce çalıştırılmalıdır.</span><span class="sxs-lookup"><span data-stu-id="fcc74-608">When deploying to IIS, CORS has to run before Windows Authentication if the server isn't configured to allow anonymous access.</span></span> <span data-ttu-id="fcc74-609">Bu senaryoyu desteklemek için, [IIS CORS modülünün](https://www.iis.net/downloads/microsoft/iis-cors-module) uygulama için yüklenmiş ve yapılandırılmış olması gerekir.</span><span class="sxs-lookup"><span data-stu-id="fcc74-609">To support this scenario, the [IIS CORS module](https://www.iis.net/downloads/microsoft/iis-cors-module) needs to be installed and configured for the app.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fcc74-610">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="fcc74-610">Additional resources</span></span>

* [<span data-ttu-id="fcc74-611">Çıkış Noktaları Arası Kaynak Paylaşımı (CORS)</span><span class="sxs-lookup"><span data-stu-id="fcc74-611">Cross-Origin Resource Sharing (CORS)</span></span>](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [<span data-ttu-id="fcc74-612">IIS CORS modülünü kullanmaya başlama</span><span class="sxs-lookup"><span data-stu-id="fcc74-612">Getting started with the IIS CORS module</span></span>](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
