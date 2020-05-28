---
<span data-ttu-id="6d68c-101">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-102">'Blazor'</span></span>
- <span data-ttu-id="6d68c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-103">'Identity'</span></span>
- <span data-ttu-id="6d68c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-105">'Razor'</span></span>
- <span data-ttu-id="6d68c-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-106">'SignalR' uid:</span></span> 

---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="6d68c-107">ASP.NET Core içinde URL yeniden yazma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="6d68c-107">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="6d68c-108">X [MIKAEL Mengistu](https://github.com/mikaelm12) tarafından</span><span class="sxs-lookup"><span data-stu-id="6d68c-108">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6d68c-109">Bu belgede, ASP.NET Core uygulamalarında URL yeniden yazma ara yazılımı kullanma yönergeleriyle birlikte URL yeniden yazma tanıtılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-109">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="6d68c-110">URL yeniden yazma, istek URL 'Lerini bir veya daha fazla önceden tanımlanmış kurala göre değiştirme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-110">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="6d68c-111">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmaması için kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-111">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="6d68c-112">URL yeniden yazma işlemi birkaç senaryoda yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="6d68c-112">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="6d68c-113">Sunucu kaynaklarını geçici olarak veya kalıcı olarak taşıyın veya değiştirin ve bu kaynakların kararlı konum belirleyicilerinin bakımını yapın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-113">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="6d68c-114">İstek işlemeyi farklı uygulamalar arasında veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="6d68c-114">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="6d68c-115">Gelen isteklerde URL segmentlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-115">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="6d68c-116">Arama motoru Iyileştirmesi (SEO) için genel URL 'Leri iyileştirin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-116">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="6d68c-117">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmeye yardımcı olmak için kolay genel URL 'Lerin kullanılmasına izin verme.</span><span class="sxs-lookup"><span data-stu-id="6d68c-117">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="6d68c-118">Güvensiz istekleri güvenli uç noktalara yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-118">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="6d68c-119">Bir dış sitenin varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı Hotlink 'i engelleyin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-119">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="6d68c-120">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-120">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="6d68c-121">Uygun yerlerde kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-121">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="6d68c-122">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6d68c-122">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="6d68c-123">URL yeniden yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="6d68c-123">URL redirect and URL rewrite</span></span>

<span data-ttu-id="6d68c-124">*URL yeniden yönlendirme* ve *URL yeniden yazma* arasındaki ifade farkı daha hafif ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-124">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="6d68c-125">ASP.NET Core URL yeniden yazma ara yazılımı her ikisine de ihtiyacı verebilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-125">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="6d68c-126">*URL yeniden yönlendirme* , istemcinin, ilk olarak istenen istemciden farklı bir adresteki kaynağa erişmesi için bir istemci tarafı işlemi içerir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-126">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="6d68c-127">Bu, sunucuya gidiş dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-127">This requires a round trip to the server.</span></span> <span data-ttu-id="6d68c-128">İstemci kaynak için yeni bir istek yaptığında, istemciye döndürülen yeniden yönlendirme URL 'SI tarayıcının adres çubuğunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-128">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="6d68c-129">`/resource`Öğesine *yönlendiriliyorsa* `/different-resource` sunucu, istemcinin, `/different-resource` yeniden yönlendirmenin geçici ya da kalıcı olduğunu belirten bir durum kodu ile kaynağı elde etmesi gerektiğini yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-129">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmeti uç noktası, sürüm 1 ' den (v1) sunucudaki sürüm 2 ' ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="6d68c-135">İstekleri farklı bir URL 'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yeniden yönlendirmenin kalıcı mi yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="6d68c-135">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="6d68c-136">*301-taşınan kalıcı* durum kodu, kaynağın yeni, kalıcı bir URL olduğu ve istemciye, gelecekteki tüm isteklerin kaynak için tüm istekleri yeni URL 'yi kullanması gerektiğini bildirmek istediğinizde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-136">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="6d68c-137">*İstemci, 301 durum kodu alındığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="6d68c-137">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="6d68c-138">*302-bulunan* durum kodu, yeniden yönlendirmenin geçici ya da genellikle değişikliğe tabi olduğu durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-138">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="6d68c-139">302 durum kodu, istemcinin URL 'YI depolayamadığını ve gelecekte kullanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-139">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="6d68c-140">Durum kodları hakkında daha fazla bilgi için bkz. [RFC 2616: durum kodu tanımları](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6d68c-140">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="6d68c-141">*URL yeniden yazma* , istenen istemciden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-141">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="6d68c-142">URL yeniden yazma, sunucuya gidiş dönüş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-142">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="6d68c-143">Yeniden yazan URL istemciye döndürülmüyor ve tarayıcının adres çubuğunda görünmüyor.</span><span class="sxs-lookup"><span data-stu-id="6d68c-143">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="6d68c-144">`/resource`Öğesine *rewritten* `/different-resource` geri dönerse, sunucu, kaynağını *dahili olarak* getirir ve döndürür `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-144">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="6d68c-145">İstemci, yeniden yazan URL 'de kaynağı alabiliyor olsa da, istemci isteği yaptığında ve yanıtı aldığında kaynağın yeniden yazan URL 'de bulunduğunu bilgilendirmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-145">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Sunucu üzerinde sürüm 1 ' den (v1) sürüm 2 ' ye (v2) bir WebAPI hizmet uç noktası değiştirilmiştir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="6d68c-150">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="6d68c-150">URL rewriting sample app</span></span>

<span data-ttu-id="6d68c-151">[Örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)birlikte YENIDEN yazma URL 'sinin özelliklerini inceleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-151">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="6d68c-152">Uygulama yeniden yönlendirme ve yeniden yazma kuralları uygular ve birkaç senaryo için yeniden yönlendirilen veya yeniden yazan URL 'YI gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-152">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="6d68c-153">URL yeniden yazma ara yazılımı ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="6d68c-153">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="6d68c-154">Aşağıdaki yaklaşımlardan birini kullandığımmdan URL yeniden yazma ara yazılımı kullanın:</span><span class="sxs-lookup"><span data-stu-id="6d68c-154">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="6d68c-155">Windows Server 'da IIS ile URL yeniden yazma modülü</span><span class="sxs-lookup"><span data-stu-id="6d68c-155">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="6d68c-156">Apache Server 'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="6d68c-156">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="6d68c-157">NGINX üzerinde URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="6d68c-157">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="6d68c-158">Ayrıca, uygulama [http. sys sunucusunda](xref:fundamentals/servers/httpsys) barındırıldığı zaman ara yazılımı kullanın (eski adıyla webListener olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="6d68c-158">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="6d68c-159">IIS, Apache ve NGINX 'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="6d68c-159">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="6d68c-160">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-160">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="6d68c-161">Sunucu modüllerinin bazı özellikleri, `IsFile` `IsDirectory` IIS yeniden yazma modülünün ve kısıtlamaları gibi ASP.NET Core projelerle birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-161">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="6d68c-162">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-162">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="6d68c-163">Ara yazılım performansı büyük olasılıkla modüllerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="6d68c-163">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="6d68c-164">Sınama, performansı en iyi şekilde düşürür veya performans düşüklüğü göz ardı edilebilir olduğundan emin olmanın tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-164">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="6d68c-165">Paket</span><span class="sxs-lookup"><span data-stu-id="6d68c-165">Package</span></span>

<span data-ttu-id="6d68c-166">URL yeniden yazma ara yazılımı, ASP.NET Core uygulamalarında örtük olarak bulunan [Microsoft. AspNetCore. yeniden yazma](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-166">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="6d68c-167">Uzantı ve Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6d68c-167">Extension and options</span></span>

<span data-ttu-id="6d68c-168">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriyle [Rewriteoptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir ÖRNEĞINI oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-168">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="6d68c-169">Birden çok kuralı, işlenmeyi istediğiniz sırada zincirle.</span><span class="sxs-lookup"><span data-stu-id="6d68c-169">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="6d68c-170">, `RewriteOptions` İle istek ardışık düzenine eklendikçe, bu URL 'ye yeniden yazma ara yazılımı ile geçirilir <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="6d68c-170">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="6d68c-171">Www olmayan www 'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-171">Redirect non-www to www</span></span>

<span data-ttu-id="6d68c-172">Üç seçenek, uygulamanın istek olmayan istekleri yeniden yönlendirmesine izin verir `www` `www` :</span><span class="sxs-lookup"><span data-stu-id="6d68c-172">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="6d68c-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: İstek değilse, istek alt etki alanına kalıcı olarak yeniden yönlendirin `www` `www` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-173"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="6d68c-174">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-174">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="6d68c-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: `www` Gelen istek değilse isteği alt etki alanına yeniden yönlendirin `www` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-175"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="6d68c-176">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-176">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="6d68c-177">Aşırı yükleme, yanıt için durum kodu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-177">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="6d68c-178"><xref:Microsoft.AspNetCore.Http.StatusCodes>Bir durum kodu ataması için sınıfın bir alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-178">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="6d68c-179">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-179">URL redirect</span></span>

<span data-ttu-id="6d68c-180"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>İstekleri yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-180">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="6d68c-181">İlk parametre gelen URL 'nin yolu ile eşleşen Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-181">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="6d68c-182">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-182">The second parameter is the replacement string.</span></span> <span data-ttu-id="6d68c-183">Varsa, üçüncü parametre durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-183">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="6d68c-184">Durum kodunu belirtmezseniz, durum kodu varsayılan olarak *302-bulunur*; bu da kaynağın geçici olarak taşındığını veya değiştirildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-184">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="6d68c-185">Geliştirici araçları etkinleştirilmiş bir tarayıcıda, örnek uygulamaya yol ile bir istek oluşturun `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-185">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="6d68c-186">Regex, üzerindeki istek yoluyla eşleşir `redirect-rule/(.*)` ve yol ile değiştirilmiştir `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-186">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="6d68c-187">Yeniden yönlendirme URL 'SI, *302 tarafından bulunan* bir durum kodu ile istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-187">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="6d68c-188">Tarayıcı, tarayıcının adres çubuğunda görüntülenen yeniden yönlendirme URL 'SI üzerinde yeni bir istek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-188">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="6d68c-189">Örnek uygulamadaki hiçbir kural, yeniden yönlendirme URL 'SI üzerinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="6d68c-189">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="6d68c-190">İkinci istek uygulamadan *200-Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-190">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="6d68c-191">Yanıtın gövdesi, yeniden yönlendirme URL 'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-191">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="6d68c-192">Bir URL *yeniden yönlendirildiğinde*sunucuya gidiş dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-192">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="6d68c-193">Yeniden yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-193">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="6d68c-194">Yeniden yönlendirme kuralları, bir yeniden yönlendirmeden sonra dahil olmak üzere, uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-194">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="6d68c-195">Yanlışlıkla *sonsuz yeniden yönlendirmeler döngüsü*oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-195">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="6d68c-196">Özgün Istek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="6d68c-196">Original Request: `/redirect-rule/1234/5678`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="6d68c-198">Parantez içinde yer alan ifadenin kısmına bir *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-198">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="6d68c-199">İfadenin nokta ( `.` ), *herhangi bir karakterle eşleşir*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-199">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="6d68c-200">Yıldız işareti ( `*` ) *, önceki karakterle sıfır veya daha fazla kez eşleşme*gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-200">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="6d68c-201">Bu nedenle, URL 'nin son iki yol kesimi, `1234/5678` yakalama grubu tarafından yakalanır `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-201">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="6d68c-202">Sonrasında istek URL 'sinde sağladığınız herhangi bir değer `redirect-rule/` Bu tek yakalama grubu tarafından yakalandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="6d68c-202">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="6d68c-203">Değiştirme dizesinde, yakalanan gruplar, dolar işareti ( `$` ) ve ardından yakalamanın sıra numarası ile birlikte dizeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-203">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="6d68c-204">İlk yakalama grubu değeri ile elde edilir `$1` , ikincisi ile `$2` ve normal Regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="6d68c-204">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="6d68c-205">Örnek uygulamadaki yeniden yönlendirme kuralı Regex bölümünde yalnızca bir tane yakalanan grup bulunur, bu nedenle değiştirme dizesinde yalnızca bir tane eklenmiş grup vardır `$1` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-205">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="6d68c-206">Kural uygulandığında, URL olur `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-206">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="6d68c-207">Güvenli bir uç noktaya URL yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="6d68c-207">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="6d68c-208"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>Http ISTEKLERINI https protokolünü kullanarak aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-208">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="6d68c-209">Durum kodu sağlanmazsa, ara yazılım varsayılan olarak *302-bulunur*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-209">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="6d68c-210">Bağlantı noktası sağlanmazsa:</span><span class="sxs-lookup"><span data-stu-id="6d68c-210">If the port isn't supplied:</span></span>

* <span data-ttu-id="6d68c-211">Ara yazılım varsayılan olarak olur `null` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-211">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="6d68c-212">Şema `https` (https Protokolü) olarak değişir ve istemci, 443 numaralı bağlantı noktasında kaynağa erişir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-212">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="6d68c-213">Aşağıdaki örnek, durum kodunun *301-kalıcı olarak taşınacağını* ve bağlantı noktasını 5001 olarak nasıl değiştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-213">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="6d68c-214"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>Güvenli olmayan istekleri, bağlantı noktası 443 üzerinde GÜVENLI https Protokolü ile aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-214">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="6d68c-215">Ara yazılım durum kodunu 301 olarak ayarlar ve *kalıcı olarak taşınır*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-215">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="6d68c-216">Ek yeniden yönlendirme kuralları gereksinimi olmadan güvenli bir uç noktaya yönlendirilirken, HTTPS yeniden yönlendirme ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-216">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="6d68c-217">Daha fazla bilgi için bkz. [https 'Yi zorla](xref:security/enforcing-ssl#require-https) konusu.</span><span class="sxs-lookup"><span data-stu-id="6d68c-217">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="6d68c-218">Örnek uygulama, veya kullanımını gösterme yeteneğine sahiptir `AddRedirectToHttps` `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-218">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="6d68c-219">Uzantı yöntemini öğesine ekleyin `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-219">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="6d68c-220">Herhangi bir URL 'de uygulamaya güvenli olmayan bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-220">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="6d68c-221">Otomatik olarak imzalanan sertifikanın güvenilmeyen tarayıcı güvenlik uyarısını kapatın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-221">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="6d68c-222">Kullanarak özgün Istek `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="6d68c-222">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="6d68c-224">Kullanarak özgün Istek `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="6d68c-224">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="6d68c-226">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="6d68c-226">URL rewrite</span></span>

<span data-ttu-id="6d68c-227"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>URL 'leri yeniden yazma kuralı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-227">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="6d68c-228">İlk parametre gelen URL yolundaki eşleşme için Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-228">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="6d68c-229">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-229">The second parameter is the replacement string.</span></span> <span data-ttu-id="6d68c-230">Üçüncü parametresi, `skipRemainingRules: {true|false}` geçerli kural uygulanmışsa ek yeniden yazma kurallarının atlanıp atlanmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-230">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="6d68c-231">Özgün Istek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="6d68c-231">Original Request: `/rewrite-rule/1234/5678`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="6d68c-233">İfadenin başındaki simgeyi seçtiğinizde ( `^` ), eşleşmesinin URL yolunun başlangıcında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-233">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="6d68c-234">Önceki örnekte, yeniden yönlendirme kuralıyla, Regex başlangıcında `redirect-rule/(.*)` simgeyi seçtiğinizde ( `^` ) yoktur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-234">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="6d68c-235">Bu nedenle, `redirect-rule/` başarılı bir eşleşme için herhangi bir karakterden önce yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-235">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="6d68c-236">Yol</span><span class="sxs-lookup"><span data-stu-id="6d68c-236">Path</span></span>                               | <span data-ttu-id="6d68c-237">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-237">Match</span></span> |
| ---
<span data-ttu-id="6d68c-238">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-238">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-239">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-239">'Blazor'</span></span>
- <span data-ttu-id="6d68c-240">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-240">'Identity'</span></span>
- <span data-ttu-id="6d68c-241">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-241">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-242">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-242">'Razor'</span></span>
- <span data-ttu-id="6d68c-243">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-243">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-244">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-244">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-245">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-245">'Blazor'</span></span>
- <span data-ttu-id="6d68c-246">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-246">'Identity'</span></span>
- <span data-ttu-id="6d68c-247">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-247">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-248">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-248">'Razor'</span></span>
- <span data-ttu-id="6d68c-249">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-249">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-250">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-250">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-251">'Blazor'</span></span>
- <span data-ttu-id="6d68c-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-252">'Identity'</span></span>
- <span data-ttu-id="6d68c-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-254">'Razor'</span></span>
- <span data-ttu-id="6d68c-255">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-256">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-256">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-257">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-257">'Blazor'</span></span>
- <span data-ttu-id="6d68c-258">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-258">'Identity'</span></span>
- <span data-ttu-id="6d68c-259">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-259">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-260">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-260">'Razor'</span></span>
- <span data-ttu-id="6d68c-261">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-261">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-262">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-262">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-263">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-263">'Blazor'</span></span>
- <span data-ttu-id="6d68c-264">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-264">'Identity'</span></span>
- <span data-ttu-id="6d68c-265">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-265">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-266">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-266">'Razor'</span></span>
- <span data-ttu-id="6d68c-267">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-267">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-268">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-268">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-269">'Blazor'</span></span>
- <span data-ttu-id="6d68c-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-270">'Identity'</span></span>
- <span data-ttu-id="6d68c-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-272">'Razor'</span></span>
- <span data-ttu-id="6d68c-273">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-274">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-274">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-275">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-275">'Blazor'</span></span>
- <span data-ttu-id="6d68c-276">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-276">'Identity'</span></span>
- <span data-ttu-id="6d68c-277">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-277">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-278">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-278">'Razor'</span></span>
- <span data-ttu-id="6d68c-279">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-279">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-280">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-280">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-281">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-281">'Blazor'</span></span>
- <span data-ttu-id="6d68c-282">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-282">'Identity'</span></span>
- <span data-ttu-id="6d68c-283">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-283">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-284">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-284">'Razor'</span></span>
- <span data-ttu-id="6d68c-285">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-285">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-286">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-286">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-287">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-287">'Blazor'</span></span>
- <span data-ttu-id="6d68c-288">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-288">'Identity'</span></span>
- <span data-ttu-id="6d68c-289">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-289">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-290">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-290">'Razor'</span></span>
- <span data-ttu-id="6d68c-291">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-291">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-292">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-292">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-293">'Blazor'</span></span>
- <span data-ttu-id="6d68c-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-294">'Identity'</span></span>
- <span data-ttu-id="6d68c-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-296">'Razor'</span></span>
- <span data-ttu-id="6d68c-297">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-298">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-298">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-299">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-299">'Blazor'</span></span>
- <span data-ttu-id="6d68c-300">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-300">'Identity'</span></span>
- <span data-ttu-id="6d68c-301">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-301">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-302">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-302">'Razor'</span></span>
- <span data-ttu-id="6d68c-303">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-303">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-304">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-304">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-305">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-305">'Blazor'</span></span>
- <span data-ttu-id="6d68c-306">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-306">'Identity'</span></span>
- <span data-ttu-id="6d68c-307">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-307">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-308">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-308">'Razor'</span></span>
- <span data-ttu-id="6d68c-309">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-309">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-310">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-310">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-311">'Blazor'</span></span>
- <span data-ttu-id="6d68c-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-312">'Identity'</span></span>
- <span data-ttu-id="6d68c-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-314">'Razor'</span></span>
- <span data-ttu-id="6d68c-315">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-316">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-316">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-317">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-317">'Blazor'</span></span>
- <span data-ttu-id="6d68c-318">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-318">'Identity'</span></span>
- <span data-ttu-id="6d68c-319">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-319">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-320">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-320">'Razor'</span></span>
- <span data-ttu-id="6d68c-321">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-321">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-322">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-322">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-323">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-323">'Blazor'</span></span>
- <span data-ttu-id="6d68c-324">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-324">'Identity'</span></span>
- <span data-ttu-id="6d68c-325">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-325">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-326">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-326">'Razor'</span></span>
- <span data-ttu-id="6d68c-327">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-327">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Evet | | `/my-cool-redirect-rule/1234/5678` | Evet | | `/anotherredirect-rule/1234/5678`  | Evet |</span><span class="sxs-lookup"><span data-stu-id="6d68c-328">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="6d68c-329">Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)` yalnızca ile başlarsa yollarla eşleşir `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-329">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="6d68c-330">Aşağıdaki tabloda, eşleşen farkı aklınızda.</span><span class="sxs-lookup"><span data-stu-id="6d68c-330">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="6d68c-331">Yol</span><span class="sxs-lookup"><span data-stu-id="6d68c-331">Path</span></span>                              | <span data-ttu-id="6d68c-332">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-332">Match</span></span> |
| ---
<span data-ttu-id="6d68c-333">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-333">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-334">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-334">'Blazor'</span></span>
- <span data-ttu-id="6d68c-335">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-335">'Identity'</span></span>
- <span data-ttu-id="6d68c-336">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-336">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-337">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-337">'Razor'</span></span>
- <span data-ttu-id="6d68c-338">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-338">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-339">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-339">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-340">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-340">'Blazor'</span></span>
- <span data-ttu-id="6d68c-341">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-341">'Identity'</span></span>
- <span data-ttu-id="6d68c-342">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-342">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-343">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-343">'Razor'</span></span>
- <span data-ttu-id="6d68c-344">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-344">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-345">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-346">'Blazor'</span></span>
- <span data-ttu-id="6d68c-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-347">'Identity'</span></span>
- <span data-ttu-id="6d68c-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-349">'Razor'</span></span>
- <span data-ttu-id="6d68c-350">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-351">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-352">'Blazor'</span></span>
- <span data-ttu-id="6d68c-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-353">'Identity'</span></span>
- <span data-ttu-id="6d68c-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-355">'Razor'</span></span>
- <span data-ttu-id="6d68c-356">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-357">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-358">'Blazor'</span></span>
- <span data-ttu-id="6d68c-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-359">'Identity'</span></span>
- <span data-ttu-id="6d68c-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-361">'Razor'</span></span>
- <span data-ttu-id="6d68c-362">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-363">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-364">'Blazor'</span></span>
- <span data-ttu-id="6d68c-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-365">'Identity'</span></span>
- <span data-ttu-id="6d68c-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-367">'Razor'</span></span>
- <span data-ttu-id="6d68c-368">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-369">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-370">'Blazor'</span></span>
- <span data-ttu-id="6d68c-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-371">'Identity'</span></span>
- <span data-ttu-id="6d68c-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-373">'Razor'</span></span>
- <span data-ttu-id="6d68c-374">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-375">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-376">'Blazor'</span></span>
- <span data-ttu-id="6d68c-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-377">'Identity'</span></span>
- <span data-ttu-id="6d68c-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-379">'Razor'</span></span>
- <span data-ttu-id="6d68c-380">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-381">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-382">'Blazor'</span></span>
- <span data-ttu-id="6d68c-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-383">'Identity'</span></span>
- <span data-ttu-id="6d68c-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-385">'Razor'</span></span>
- <span data-ttu-id="6d68c-386">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-387">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-388">'Blazor'</span></span>
- <span data-ttu-id="6d68c-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-389">'Identity'</span></span>
- <span data-ttu-id="6d68c-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-391">'Razor'</span></span>
- <span data-ttu-id="6d68c-392">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-393">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-394">'Blazor'</span></span>
- <span data-ttu-id="6d68c-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-395">'Identity'</span></span>
- <span data-ttu-id="6d68c-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-397">'Razor'</span></span>
- <span data-ttu-id="6d68c-398">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-399">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-400">'Blazor'</span></span>
- <span data-ttu-id="6d68c-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-401">'Identity'</span></span>
- <span data-ttu-id="6d68c-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-403">'Razor'</span></span>
- <span data-ttu-id="6d68c-404">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-405">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-406">'Blazor'</span></span>
- <span data-ttu-id="6d68c-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-407">'Identity'</span></span>
- <span data-ttu-id="6d68c-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-409">'Razor'</span></span>
- <span data-ttu-id="6d68c-410">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-411">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-412">'Blazor'</span></span>
- <span data-ttu-id="6d68c-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-413">'Identity'</span></span>
- <span data-ttu-id="6d68c-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-415">'Razor'</span></span>
- <span data-ttu-id="6d68c-416">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-416">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Evet | | `/my-cool-rewrite-rule/1234/5678` | Hayır | | `/anotherrewrite-rule/1234/5678`  | Hayır |</span><span class="sxs-lookup"><span data-stu-id="6d68c-417">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="6d68c-418">`^rewrite-rule/`İfadenin bölümünü takip eden iki yakalama grubu vardır `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-418">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="6d68c-419">`\d`Belirtir *bir sayıyla (sayı) eşleşir*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-419">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="6d68c-420">Artı işareti ( `+` ) *bir veya daha fazla önceki karakterden eşleşiyor*demektir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-420">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="6d68c-421">Bu nedenle, URL bir sayı içermeli ve ardından İleri eğik çizgi ve ardından başka bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-421">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="6d68c-422">Bu yakalama grupları, ve olarak yeniden yazan URL 'sine `$1` eklenir `$2` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-422">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="6d68c-423">Yeniden yazma kuralı değiştirme dizesi yakalanan grupları sorgu dizesine koyar.</span><span class="sxs-lookup"><span data-stu-id="6d68c-423">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="6d68c-424">İstenen yolu, `/rewrite-rule/1234/5678` kaynağı elde etmek için yeniden yazılır `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-424">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="6d68c-425">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazdığınızda korunur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-425">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="6d68c-426">Kaynağı almak için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="6d68c-426">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="6d68c-427">Kaynak varsa, bu, alınır ve istemciye *200-ok* durum kodu ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6d68c-427">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="6d68c-428">İstemci yeniden yönlendirmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-428">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="6d68c-429">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamaz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-429">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="6d68c-430">`skipRemainingRules: true`Eşleşen kuralların hesaplama maliyeti ve uygulama yanıt süresini arttığı için mümkün olan her durumda kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-430">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="6d68c-431">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="6d68c-431">For the fastest app response:</span></span>
>
> * <span data-ttu-id="6d68c-432">En sık eşleşen kuraldan en az sıklıkta eşleşen kurala göre yeniden yazma kuralları.</span><span class="sxs-lookup"><span data-stu-id="6d68c-432">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="6d68c-433">Bir eşleşme gerçekleştiğinde ve ek kural işleme gerekli olmadığında kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-433">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="6d68c-434">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="6d68c-434">Apache mod_rewrite</span></span>

<span data-ttu-id="6d68c-435">İle Apache mod_rewrite kuralları uygulayın <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-435">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="6d68c-436">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-436">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="6d68c-437">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için bkz. [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="6d68c-437">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="6d68c-438">, <xref:System.IO.StreamReader> *ApacheModRewrite. txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6d68c-438">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="6d68c-439">Örnek uygulama, istekleri ' den ' e yeniden yönlendirir `/apache-mod-rules-redirect/(.\*)` `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-439">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="6d68c-440">Yanıt durum kodu *302-bulundu*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-440">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="6d68c-441">Özgün Istek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="6d68c-441">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="6d68c-443">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="6d68c-443">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="6d68c-444">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-444">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="6d68c-445">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="6d68c-445">HTTP_ACCEPT</span></span>
* <span data-ttu-id="6d68c-446">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="6d68c-446">HTTP_CONNECTION</span></span>
* <span data-ttu-id="6d68c-447">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="6d68c-447">HTTP_COOKIE</span></span>
* <span data-ttu-id="6d68c-448">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="6d68c-448">HTTP_FORWARDED</span></span>
* <span data-ttu-id="6d68c-449">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="6d68c-449">HTTP_HOST</span></span>
* <span data-ttu-id="6d68c-450">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="6d68c-450">HTTP_REFERER</span></span>
* <span data-ttu-id="6d68c-451">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="6d68c-451">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="6d68c-452">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6d68c-452">HTTPS</span></span>
* <span data-ttu-id="6d68c-453">IPV6</span><span class="sxs-lookup"><span data-stu-id="6d68c-453">IPV6</span></span>
* <span data-ttu-id="6d68c-454">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="6d68c-454">QUERY_STRING</span></span>
* <span data-ttu-id="6d68c-455">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-455">REMOTE_ADDR</span></span>
* <span data-ttu-id="6d68c-456">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="6d68c-456">REMOTE_PORT</span></span>
* <span data-ttu-id="6d68c-457">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="6d68c-457">REQUEST_FILENAME</span></span>
* <span data-ttu-id="6d68c-458">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="6d68c-458">REQUEST_METHOD</span></span>
* <span data-ttu-id="6d68c-459">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="6d68c-459">REQUEST_SCHEME</span></span>
* <span data-ttu-id="6d68c-460">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="6d68c-460">REQUEST_URI</span></span>
* <span data-ttu-id="6d68c-461">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="6d68c-461">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="6d68c-462">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-462">SERVER_ADDR</span></span>
* <span data-ttu-id="6d68c-463">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="6d68c-463">SERVER_PORT</span></span>
* <span data-ttu-id="6d68c-464">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="6d68c-464">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="6d68c-465">TIME</span><span class="sxs-lookup"><span data-stu-id="6d68c-465">TIME</span></span>
* <span data-ttu-id="6d68c-466">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="6d68c-466">TIME_DAY</span></span>
* <span data-ttu-id="6d68c-467">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="6d68c-467">TIME_HOUR</span></span>
* <span data-ttu-id="6d68c-468">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="6d68c-468">TIME_MIN</span></span>
* <span data-ttu-id="6d68c-469">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="6d68c-469">TIME_MON</span></span>
* <span data-ttu-id="6d68c-470">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="6d68c-470">TIME_SEC</span></span>
* <span data-ttu-id="6d68c-471">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="6d68c-471">TIME_WDAY</span></span>
* <span data-ttu-id="6d68c-472">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="6d68c-472">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="6d68c-473">IIS URL yeniden yazma modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="6d68c-473">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="6d68c-474">IIS URL yeniden yazma modülü için geçerli olan kural kümesini kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-474">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="6d68c-475">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-475">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="6d68c-476">Windows Server IIS 'de çalışırken uygulamanın *Web. config* dosyasını kullanmak için ara yazılımı yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-476">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="6d68c-477">IIS ile, IIS yeniden yazma modülüyle çakışmalardan kaçınmak için bu kuralların uygulamanın *Web. config* dosyası dışında depolanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-477">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="6d68c-478">Daha fazla bilgi ve IIS URL yeniden yazma modülü kurallarının örnekleri için bkz. [Using URL yeniden yazma modülü 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) ve [URL yeniden yazma modülü yapılandırma başvurusu](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="6d68c-478">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="6d68c-479">, <xref:System.IO.StreamReader> *Iisurlyeniden yazma. xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6d68c-479">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="6d68c-480">Örnek uygulama, ' den ' e olan istekleri yeniden yazar `/iis-rules-rewrite/(.*)` `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-480">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="6d68c-481">Yanıt, istemciye *200-ok* durum kodu ile gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-481">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="6d68c-482">Özgün Istek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="6d68c-482">Original Request: `/iis-rules-rewrite/1234`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="6d68c-484">Uygulamanızı istenmeyen yollarla etkileyebilecek sunucu düzeyi kurallara sahip etkin bir IIS yeniden yazma modülünüzün olması halinde, bir uygulama için IIS yeniden yazma modülünü devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-484">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="6d68c-485">Daha fazla bilgi için bkz. [IIS modüllerini devre dışı bırakma](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="6d68c-485">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="6d68c-486">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="6d68c-486">Unsupported features</span></span>

<span data-ttu-id="6d68c-487">ASP.NET Core 2. x ile yayınlanan ara yazılım, aşağıdaki IIS URL yeniden yazma modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="6d68c-487">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="6d68c-488">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="6d68c-488">Outbound Rules</span></span>
* <span data-ttu-id="6d68c-489">Özel sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="6d68c-489">Custom Server Variables</span></span>
* <span data-ttu-id="6d68c-490">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="6d68c-490">Wildcards</span></span>
* <span data-ttu-id="6d68c-491">LogRewrittenUrl 'Si</span><span class="sxs-lookup"><span data-stu-id="6d68c-491">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="6d68c-492">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="6d68c-492">Supported server variables</span></span>

<span data-ttu-id="6d68c-493">Ara yazılım aşağıdaki IIS URL yeniden yazma modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="6d68c-493">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="6d68c-494">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="6d68c-494">CONTENT_LENGTH</span></span>
* <span data-ttu-id="6d68c-495">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="6d68c-495">CONTENT_TYPE</span></span>
* <span data-ttu-id="6d68c-496">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="6d68c-496">HTTP_ACCEPT</span></span>
* <span data-ttu-id="6d68c-497">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="6d68c-497">HTTP_CONNECTION</span></span>
* <span data-ttu-id="6d68c-498">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="6d68c-498">HTTP_COOKIE</span></span>
* <span data-ttu-id="6d68c-499">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="6d68c-499">HTTP_HOST</span></span>
* <span data-ttu-id="6d68c-500">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="6d68c-500">HTTP_REFERER</span></span>
* <span data-ttu-id="6d68c-501">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="6d68c-501">HTTP_URL</span></span>
* <span data-ttu-id="6d68c-502">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="6d68c-502">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="6d68c-503">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6d68c-503">HTTPS</span></span>
* <span data-ttu-id="6d68c-504">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-504">LOCAL_ADDR</span></span>
* <span data-ttu-id="6d68c-505">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="6d68c-505">QUERY_STRING</span></span>
* <span data-ttu-id="6d68c-506">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-506">REMOTE_ADDR</span></span>
* <span data-ttu-id="6d68c-507">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="6d68c-507">REMOTE_PORT</span></span>
* <span data-ttu-id="6d68c-508">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="6d68c-508">REQUEST_FILENAME</span></span>
* <span data-ttu-id="6d68c-509">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="6d68c-509">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="6d68c-510">Ayrıca bir ile elde edebilirsiniz <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-510">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="6d68c-511">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-511">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="6d68c-512">Yeniden yazma kuralları dosyalarınızın sağladığınız yoldaki sunucuya dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-512">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="6d68c-513">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="6d68c-513">Method-based rule</span></span>

<span data-ttu-id="6d68c-514"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Bir yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-514">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="6d68c-515">`Add`, <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> metodunda kullanım için kullanılabilir hale getiren öğesini gösterir <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-515">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="6d68c-516">[Rewritecontext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) , ek ardışık düzen işlemenin nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="6d68c-516">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="6d68c-517">Değeri <xref:Microsoft.AspNetCore.Rewrite.RuleResult> Aşağıdaki tabloda açıklanan alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-517">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="6d68c-518">Eylem</span><span class="sxs-lookup"><span data-stu-id="6d68c-518">Action</span></span>                                                           |
| ---
<span data-ttu-id="6d68c-519">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-519">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-520">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-520">'Blazor'</span></span>
- <span data-ttu-id="6d68c-521">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-521">'Identity'</span></span>
- <span data-ttu-id="6d68c-522">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-522">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-523">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-523">'Razor'</span></span>
- <span data-ttu-id="6d68c-524">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-524">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-525">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-525">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-526">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-526">'Blazor'</span></span>
- <span data-ttu-id="6d68c-527">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-527">'Identity'</span></span>
- <span data-ttu-id="6d68c-528">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-528">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-529">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-529">'Razor'</span></span>
- <span data-ttu-id="6d68c-530">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-530">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-531">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-531">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-532">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-532">'Blazor'</span></span>
- <span data-ttu-id="6d68c-533">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-533">'Identity'</span></span>
- <span data-ttu-id="6d68c-534">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-534">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-535">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-535">'Razor'</span></span>
- <span data-ttu-id="6d68c-536">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-536">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-537">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-537">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-538">'Blazor'</span></span>
- <span data-ttu-id="6d68c-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-539">'Identity'</span></span>
- <span data-ttu-id="6d68c-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-541">'Razor'</span></span>
- <span data-ttu-id="6d68c-542">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-543">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-543">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-544">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-544">'Blazor'</span></span>
- <span data-ttu-id="6d68c-545">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-545">'Identity'</span></span>
- <span data-ttu-id="6d68c-546">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-546">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-547">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-547">'Razor'</span></span>
- <span data-ttu-id="6d68c-548">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-548">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-549">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-549">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-550">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-550">'Blazor'</span></span>
- <span data-ttu-id="6d68c-551">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-551">'Identity'</span></span>
- <span data-ttu-id="6d68c-552">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-552">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-553">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-553">'Razor'</span></span>
- <span data-ttu-id="6d68c-554">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-554">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-555">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-555">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-556">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-556">'Blazor'</span></span>
- <span data-ttu-id="6d68c-557">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-557">'Identity'</span></span>
- <span data-ttu-id="6d68c-558">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-558">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-559">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-559">'Razor'</span></span>
- <span data-ttu-id="6d68c-560">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-560">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-561">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-561">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-562">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-562">'Blazor'</span></span>
- <span data-ttu-id="6d68c-563">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-563">'Identity'</span></span>
- <span data-ttu-id="6d68c-564">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-564">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-565">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-565">'Razor'</span></span>
- <span data-ttu-id="6d68c-566">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-566">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-567">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-567">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-568">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-568">'Blazor'</span></span>
- <span data-ttu-id="6d68c-569">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-569">'Identity'</span></span>
- <span data-ttu-id="6d68c-570">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-570">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-571">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-571">'Razor'</span></span>
- <span data-ttu-id="6d68c-572">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-572">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-573">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-573">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-574">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-574">'Blazor'</span></span>
- <span data-ttu-id="6d68c-575">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-575">'Identity'</span></span>
- <span data-ttu-id="6d68c-576">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-576">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-577">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-577">'Razor'</span></span>
- <span data-ttu-id="6d68c-578">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-578">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-579">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-579">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-580">'Blazor'</span></span>
- <span data-ttu-id="6d68c-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-581">'Identity'</span></span>
- <span data-ttu-id="6d68c-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-583">'Razor'</span></span>
- <span data-ttu-id="6d68c-584">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-585">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-585">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-586">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-586">'Blazor'</span></span>
- <span data-ttu-id="6d68c-587">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-587">'Identity'</span></span>
- <span data-ttu-id="6d68c-588">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-588">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-589">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-589">'Razor'</span></span>
- <span data-ttu-id="6d68c-590">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-590">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-591">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-591">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-592">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-592">'Blazor'</span></span>
- <span data-ttu-id="6d68c-593">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-593">'Identity'</span></span>
- <span data-ttu-id="6d68c-594">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-594">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-595">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-595">'Razor'</span></span>
- <span data-ttu-id="6d68c-596">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-596">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-597">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-597">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-598">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-598">'Blazor'</span></span>
- <span data-ttu-id="6d68c-599">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-599">'Identity'</span></span>
- <span data-ttu-id="6d68c-600">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-600">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-601">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-601">'Razor'</span></span>
- <span data-ttu-id="6d68c-602">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-602">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-603">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-603">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-604">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-604">'Blazor'</span></span>
- <span data-ttu-id="6d68c-605">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-605">'Identity'</span></span>
- <span data-ttu-id="6d68c-606">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-606">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-607">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-607">'Razor'</span></span>
- <span data-ttu-id="6d68c-608">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-608">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-609">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-609">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-610">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-610">'Blazor'</span></span>
- <span data-ttu-id="6d68c-611">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-611">'Identity'</span></span>
- <span data-ttu-id="6d68c-612">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-612">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-613">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-613">'Razor'</span></span>
- <span data-ttu-id="6d68c-614">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-614">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-615">------------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-615">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-616">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-616">'Blazor'</span></span>
- <span data-ttu-id="6d68c-617">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-617">'Identity'</span></span>
- <span data-ttu-id="6d68c-618">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-618">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-619">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-619">'Razor'</span></span>
- <span data-ttu-id="6d68c-620">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-620">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-621">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-621">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-622">'Blazor'</span></span>
- <span data-ttu-id="6d68c-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-623">'Identity'</span></span>
- <span data-ttu-id="6d68c-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-625">'Razor'</span></span>
- <span data-ttu-id="6d68c-626">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-627">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-627">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-628">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-628">'Blazor'</span></span>
- <span data-ttu-id="6d68c-629">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-629">'Identity'</span></span>
- <span data-ttu-id="6d68c-630">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-630">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-631">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-631">'Razor'</span></span>
- <span data-ttu-id="6d68c-632">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-632">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-633">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-633">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-634">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-634">'Blazor'</span></span>
- <span data-ttu-id="6d68c-635">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-635">'Identity'</span></span>
- <span data-ttu-id="6d68c-636">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-636">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-637">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-637">'Razor'</span></span>
- <span data-ttu-id="6d68c-638">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-638">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-639">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-639">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-640">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-640">'Blazor'</span></span>
- <span data-ttu-id="6d68c-641">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-641">'Identity'</span></span>
- <span data-ttu-id="6d68c-642">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-642">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-643">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-643">'Razor'</span></span>
- <span data-ttu-id="6d68c-644">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-644">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-645">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-645">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-646">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-646">'Blazor'</span></span>
- <span data-ttu-id="6d68c-647">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-647">'Identity'</span></span>
- <span data-ttu-id="6d68c-648">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-648">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-649">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-649">'Razor'</span></span>
- <span data-ttu-id="6d68c-650">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-650">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-651">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-651">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-652">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-652">'Blazor'</span></span>
- <span data-ttu-id="6d68c-653">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-653">'Identity'</span></span>
- <span data-ttu-id="6d68c-654">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-654">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-655">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-655">'Razor'</span></span>
- <span data-ttu-id="6d68c-656">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-656">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-657">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-657">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-658">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-658">'Blazor'</span></span>
- <span data-ttu-id="6d68c-659">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-659">'Identity'</span></span>
- <span data-ttu-id="6d68c-660">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-660">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-661">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-661">'Razor'</span></span>
- <span data-ttu-id="6d68c-662">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-662">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-663">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-663">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-664">'Blazor'</span></span>
- <span data-ttu-id="6d68c-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-665">'Identity'</span></span>
- <span data-ttu-id="6d68c-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-667">'Razor'</span></span>
- <span data-ttu-id="6d68c-668">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-668">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-669">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-669">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-670">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-670">'Blazor'</span></span>
- <span data-ttu-id="6d68c-671">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-671">'Identity'</span></span>
- <span data-ttu-id="6d68c-672">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-672">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-673">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-673">'Razor'</span></span>
- <span data-ttu-id="6d68c-674">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-674">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-675">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-675">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-676">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-676">'Blazor'</span></span>
- <span data-ttu-id="6d68c-677">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-677">'Identity'</span></span>
- <span data-ttu-id="6d68c-678">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-678">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-679">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-679">'Razor'</span></span>
- <span data-ttu-id="6d68c-680">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-680">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-681">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-681">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-682">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-682">'Blazor'</span></span>
- <span data-ttu-id="6d68c-683">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-683">'Identity'</span></span>
- <span data-ttu-id="6d68c-684">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-684">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-685">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-685">'Razor'</span></span>
- <span data-ttu-id="6d68c-686">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-686">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-687">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-687">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-688">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-688">'Blazor'</span></span>
- <span data-ttu-id="6d68c-689">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-689">'Identity'</span></span>
- <span data-ttu-id="6d68c-690">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-690">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-691">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-691">'Razor'</span></span>
- <span data-ttu-id="6d68c-692">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-692">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-693">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-693">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-694">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-694">'Blazor'</span></span>
- <span data-ttu-id="6d68c-695">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-695">'Identity'</span></span>
- <span data-ttu-id="6d68c-696">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-696">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-697">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-697">'Razor'</span></span>
- <span data-ttu-id="6d68c-698">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-698">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-699">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-699">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-700">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-700">'Blazor'</span></span>
- <span data-ttu-id="6d68c-701">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-701">'Identity'</span></span>
- <span data-ttu-id="6d68c-702">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-702">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-703">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-703">'Razor'</span></span>
- <span data-ttu-id="6d68c-704">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-704">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-705">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-705">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-706">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-706">'Blazor'</span></span>
- <span data-ttu-id="6d68c-707">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-707">'Identity'</span></span>
- <span data-ttu-id="6d68c-708">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-708">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-709">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-709">'Razor'</span></span>
- <span data-ttu-id="6d68c-710">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-710">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-711">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-711">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-712">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-712">'Blazor'</span></span>
- <span data-ttu-id="6d68c-713">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-713">'Identity'</span></span>
- <span data-ttu-id="6d68c-714">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-714">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-715">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-715">'Razor'</span></span>
- <span data-ttu-id="6d68c-716">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-716">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-717">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-717">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-718">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-718">'Blazor'</span></span>
- <span data-ttu-id="6d68c-719">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-719">'Identity'</span></span>
- <span data-ttu-id="6d68c-720">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-720">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-721">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-721">'Razor'</span></span>
- <span data-ttu-id="6d68c-722">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-722">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-723">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-723">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-724">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-724">'Blazor'</span></span>
- <span data-ttu-id="6d68c-725">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-725">'Identity'</span></span>
- <span data-ttu-id="6d68c-726">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-726">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-727">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-727">'Razor'</span></span>
- <span data-ttu-id="6d68c-728">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-728">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-729">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-729">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-730">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-730">'Blazor'</span></span>
- <span data-ttu-id="6d68c-731">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-731">'Identity'</span></span>
- <span data-ttu-id="6d68c-732">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-732">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-733">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-733">'Razor'</span></span>
- <span data-ttu-id="6d68c-734">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-734">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-735">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-735">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-736">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-736">'Blazor'</span></span>
- <span data-ttu-id="6d68c-737">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-737">'Identity'</span></span>
- <span data-ttu-id="6d68c-738">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-738">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-739">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-739">'Razor'</span></span>
- <span data-ttu-id="6d68c-740">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-740">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-741">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-741">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-742">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-742">'Blazor'</span></span>
- <span data-ttu-id="6d68c-743">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-743">'Identity'</span></span>
- <span data-ttu-id="6d68c-744">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-744">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-745">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-745">'Razor'</span></span>
- <span data-ttu-id="6d68c-746">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-746">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-747">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-747">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-748">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-748">'Blazor'</span></span>
- <span data-ttu-id="6d68c-749">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-749">'Identity'</span></span>
- <span data-ttu-id="6d68c-750">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-750">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-751">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-751">'Razor'</span></span>
- <span data-ttu-id="6d68c-752">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-752">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-753">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-753">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-754">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-754">'Blazor'</span></span>
- <span data-ttu-id="6d68c-755">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-755">'Identity'</span></span>
- <span data-ttu-id="6d68c-756">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-756">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-757">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-757">'Razor'</span></span>
- <span data-ttu-id="6d68c-758">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-758">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-759">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-759">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-760">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-760">'Blazor'</span></span>
- <span data-ttu-id="6d68c-761">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-761">'Identity'</span></span>
- <span data-ttu-id="6d68c-762">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-762">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-763">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-763">'Razor'</span></span>
- <span data-ttu-id="6d68c-764">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-764">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-765">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-765">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-766">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-766">'Blazor'</span></span>
- <span data-ttu-id="6d68c-767">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-767">'Identity'</span></span>
- <span data-ttu-id="6d68c-768">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-768">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-769">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-769">'Razor'</span></span>
- <span data-ttu-id="6d68c-770">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-770">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-771">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-771">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-772">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-772">'Blazor'</span></span>
- <span data-ttu-id="6d68c-773">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-773">'Identity'</span></span>
- <span data-ttu-id="6d68c-774">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-774">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-775">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-775">'Razor'</span></span>
- <span data-ttu-id="6d68c-776">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-776">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-777">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-777">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-778">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-778">'Blazor'</span></span>
- <span data-ttu-id="6d68c-779">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-779">'Identity'</span></span>
- <span data-ttu-id="6d68c-780">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-780">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-781">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-781">'Razor'</span></span>
- <span data-ttu-id="6d68c-782">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-782">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-783">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-783">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-784">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-784">'Blazor'</span></span>
- <span data-ttu-id="6d68c-785">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-785">'Identity'</span></span>
- <span data-ttu-id="6d68c-786">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-786">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-787">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-787">'Razor'</span></span>
- <span data-ttu-id="6d68c-788">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-788">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-789">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-789">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-790">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-790">'Blazor'</span></span>
- <span data-ttu-id="6d68c-791">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-791">'Identity'</span></span>
- <span data-ttu-id="6d68c-792">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-792">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-793">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-793">'Razor'</span></span>
- <span data-ttu-id="6d68c-794">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-794">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-795">-------------------------------- | | `RuleResult.ContinueRules`(varsayılan) | Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-795">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="6d68c-796">| | `RuleResult.EndResponse`             | Kuralları uygulamayı durdurun ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-796">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="6d68c-797">| | `RuleResult.SkipRemainingRules`      | Kuralları uygulamayı durdurun ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-797">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="6d68c-798">Örnek uygulama, *. xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-798">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="6d68c-799">İçin bir istek yapılırsa `/file.xml` , istek öğesine yeniden yönlendirilir `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-799">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="6d68c-800">Durum kodu *301*olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-800">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="6d68c-801">Tarayıcı */xmlfiles/File.xml*için yeni bir istek yaptığında, statik dosya ara yazılımı dosyayı *Wwwroot/xmlfiles* klasöründen istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="6d68c-801">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="6d68c-802">Yeniden yönlendirme için, yanıtın durum kodunu açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-802">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="6d68c-803">Aksi takdirde, *200-ok* durum kodu döndürülür ve yeniden yönlendirme istemcide gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-803">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="6d68c-804">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d68c-804">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="6d68c-805">Bu yaklaşım ayrıca istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-805">This approach can also rewrite requests.</span></span> <span data-ttu-id="6d68c-806">Örnek uygulama, *dosya. txt* metin dosyasına *Wwwroot* klasöründen hizmeti sağlamak için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-806">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="6d68c-807">Statik dosya ara yazılımı, güncelleştirilmiş istek yoluna göre dosyayı sunar:</span><span class="sxs-lookup"><span data-stu-id="6d68c-807">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="6d68c-808">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d68c-808">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="6d68c-809">Irule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="6d68c-809">IRule-based rule</span></span>

<span data-ttu-id="6d68c-810"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Arabirimini uygulayan bir sınıfta kural mantığını kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IRule> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-810">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="6d68c-811">`IRule`Yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="6d68c-811">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="6d68c-812">Uygulama sınıfınız, yöntemi için parametreleri geçirebilmeniz için bir Oluşturucu içerebilir <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-812">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="6d68c-813">Ve için örnek uygulamadaki parametrelerin değerleri, `extension` `newPath` çeşitli koşullara uyacak şekilde denetlenir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-813">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="6d68c-814">`extension`Bir değer içermeli ve değer *. png*, *. jpg*veya *. gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-814">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="6d68c-815">`newPath`Geçerli değilse, bir oluşturulur <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-815">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="6d68c-816">*Image. png*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-816">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="6d68c-817">*Image. jpg*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-817">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="6d68c-818">Durum kodu 301 olarak ayarlanır ve *kalıcı olarak taşınır*ve `context.Result` kuralları işlemeyi durdur ve yanıtı gönder olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-818">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="6d68c-819">Özgün Istek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="6d68c-819">Original Request: `/image.png`</span></span>

![Görüntü. png için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="6d68c-821">Özgün Istek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="6d68c-821">Original Request: `/image.jpg`</span></span>

![Image. jpg için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="6d68c-823">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="6d68c-823">Regex examples</span></span>

| <span data-ttu-id="6d68c-824">Hedef</span><span class="sxs-lookup"><span data-stu-id="6d68c-824">Goal</span></span> | <span data-ttu-id="6d68c-825">Regex dize &</span><span class="sxs-lookup"><span data-stu-id="6d68c-825">Regex String &</span></span><br><span data-ttu-id="6d68c-826">Match örneği</span><span class="sxs-lookup"><span data-stu-id="6d68c-826">Match Example</span></span> | <span data-ttu-id="6d68c-827">Değiştirme dizesi &</span><span class="sxs-lookup"><span data-stu-id="6d68c-827">Replacement String &</span></span><br><span data-ttu-id="6d68c-828">Çıkış örneği</span><span class="sxs-lookup"><span data-stu-id="6d68c-828">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="6d68c-829">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-829">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-830">'Blazor'</span></span>
- <span data-ttu-id="6d68c-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-831">'Identity'</span></span>
- <span data-ttu-id="6d68c-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-833">'Razor'</span></span>
- <span data-ttu-id="6d68c-834">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-835">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-835">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-836">'Blazor'</span></span>
- <span data-ttu-id="6d68c-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-837">'Identity'</span></span>
- <span data-ttu-id="6d68c-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-839">'Razor'</span></span>
- <span data-ttu-id="6d68c-840">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-840">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-841">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-841">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-842">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-842">'Blazor'</span></span>
- <span data-ttu-id="6d68c-843">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-843">'Identity'</span></span>
- <span data-ttu-id="6d68c-844">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-844">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-845">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-845">'Razor'</span></span>
- <span data-ttu-id="6d68c-846">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-846">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-847">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-847">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-848">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-848">'Blazor'</span></span>
- <span data-ttu-id="6d68c-849">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-849">'Identity'</span></span>
- <span data-ttu-id="6d68c-850">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-850">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-851">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-851">'Razor'</span></span>
- <span data-ttu-id="6d68c-852">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-852">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-853">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-853">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-854">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-854">'Blazor'</span></span>
- <span data-ttu-id="6d68c-855">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-855">'Identity'</span></span>
- <span data-ttu-id="6d68c-856">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-856">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-857">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-857">'Razor'</span></span>
- <span data-ttu-id="6d68c-858">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-858">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-859">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-859">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-860">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-860">'Blazor'</span></span>
- <span data-ttu-id="6d68c-861">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-861">'Identity'</span></span>
- <span data-ttu-id="6d68c-862">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-862">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-863">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-863">'Razor'</span></span>
- <span data-ttu-id="6d68c-864">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-864">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-865">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-865">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-866">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-866">'Blazor'</span></span>
- <span data-ttu-id="6d68c-867">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-867">'Identity'</span></span>
- <span data-ttu-id="6d68c-868">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-868">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-869">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-869">'Razor'</span></span>
- <span data-ttu-id="6d68c-870">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-870">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-871">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-871">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-872">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-872">'Blazor'</span></span>
- <span data-ttu-id="6d68c-873">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-873">'Identity'</span></span>
- <span data-ttu-id="6d68c-874">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-874">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-875">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-875">'Razor'</span></span>
- <span data-ttu-id="6d68c-876">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-876">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-877">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-877">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-878">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-878">'Blazor'</span></span>
- <span data-ttu-id="6d68c-879">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-879">'Identity'</span></span>
- <span data-ttu-id="6d68c-880">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-880">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-881">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-881">'Razor'</span></span>
- <span data-ttu-id="6d68c-882">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-882">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-883">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-883">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-884">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-884">'Blazor'</span></span>
- <span data-ttu-id="6d68c-885">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-885">'Identity'</span></span>
- <span data-ttu-id="6d68c-886">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-886">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-887">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-887">'Razor'</span></span>
- <span data-ttu-id="6d68c-888">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-888">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-889">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-889">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-890">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-890">'Blazor'</span></span>
- <span data-ttu-id="6d68c-891">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-891">'Identity'</span></span>
- <span data-ttu-id="6d68c-892">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-892">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-893">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-893">'Razor'</span></span>
- <span data-ttu-id="6d68c-894">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-894">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-895">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-895">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-896">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-896">'Blazor'</span></span>
- <span data-ttu-id="6d68c-897">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-897">'Identity'</span></span>
- <span data-ttu-id="6d68c-898">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-898">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-899">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-899">'Razor'</span></span>
- <span data-ttu-id="6d68c-900">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-900">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-901">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-901">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-902">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-902">'Blazor'</span></span>
- <span data-ttu-id="6d68c-903">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-903">'Identity'</span></span>
- <span data-ttu-id="6d68c-904">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-904">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-905">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-905">'Razor'</span></span>
- <span data-ttu-id="6d68c-906">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-906">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-907">---------------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-907">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-908">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-908">'Blazor'</span></span>
- <span data-ttu-id="6d68c-909">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-909">'Identity'</span></span>
- <span data-ttu-id="6d68c-910">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-910">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-911">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-911">'Razor'</span></span>
- <span data-ttu-id="6d68c-912">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-912">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-913">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-913">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-914">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-914">'Blazor'</span></span>
- <span data-ttu-id="6d68c-915">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-915">'Identity'</span></span>
- <span data-ttu-id="6d68c-916">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-916">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-917">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-917">'Razor'</span></span>
- <span data-ttu-id="6d68c-918">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-918">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-919">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-919">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-920">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-920">'Blazor'</span></span>
- <span data-ttu-id="6d68c-921">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-921">'Identity'</span></span>
- <span data-ttu-id="6d68c-922">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-922">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-923">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-923">'Razor'</span></span>
- <span data-ttu-id="6d68c-924">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-924">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-925">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-925">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-926">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-926">'Blazor'</span></span>
- <span data-ttu-id="6d68c-927">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-927">'Identity'</span></span>
- <span data-ttu-id="6d68c-928">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-928">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-929">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-929">'Razor'</span></span>
- <span data-ttu-id="6d68c-930">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-930">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-931">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-931">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-932">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-932">'Blazor'</span></span>
- <span data-ttu-id="6d68c-933">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-933">'Identity'</span></span>
- <span data-ttu-id="6d68c-934">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-934">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-935">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-935">'Razor'</span></span>
- <span data-ttu-id="6d68c-936">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-936">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-937">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-937">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-938">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-938">'Blazor'</span></span>
- <span data-ttu-id="6d68c-939">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-939">'Identity'</span></span>
- <span data-ttu-id="6d68c-940">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-940">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-941">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-941">'Razor'</span></span>
- <span data-ttu-id="6d68c-942">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-942">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-943">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-943">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-944">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-944">'Blazor'</span></span>
- <span data-ttu-id="6d68c-945">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-945">'Identity'</span></span>
- <span data-ttu-id="6d68c-946">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-946">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-947">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-947">'Razor'</span></span>
- <span data-ttu-id="6d68c-948">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-948">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-949">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-949">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-950">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-950">'Blazor'</span></span>
- <span data-ttu-id="6d68c-951">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-951">'Identity'</span></span>
- <span data-ttu-id="6d68c-952">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-952">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-953">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-953">'Razor'</span></span>
- <span data-ttu-id="6d68c-954">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-954">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-955">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-955">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-956">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-956">'Blazor'</span></span>
- <span data-ttu-id="6d68c-957">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-957">'Identity'</span></span>
- <span data-ttu-id="6d68c-958">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-958">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-959">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-959">'Razor'</span></span>
- <span data-ttu-id="6d68c-960">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-960">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-961">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-961">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-962">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-962">'Blazor'</span></span>
- <span data-ttu-id="6d68c-963">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-963">'Identity'</span></span>
- <span data-ttu-id="6d68c-964">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-964">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-965">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-965">'Razor'</span></span>
- <span data-ttu-id="6d68c-966">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-966">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-967">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-967">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-968">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-968">'Blazor'</span></span>
- <span data-ttu-id="6d68c-969">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-969">'Identity'</span></span>
- <span data-ttu-id="6d68c-970">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-970">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-971">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-971">'Razor'</span></span>
- <span data-ttu-id="6d68c-972">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-972">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-973">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-973">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-974">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-974">'Blazor'</span></span>
- <span data-ttu-id="6d68c-975">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-975">'Identity'</span></span>
- <span data-ttu-id="6d68c-976">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-976">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-977">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-977">'Razor'</span></span>
- <span data-ttu-id="6d68c-978">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-978">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-979">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-979">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-980">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-980">'Blazor'</span></span>
- <span data-ttu-id="6d68c-981">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-981">'Identity'</span></span>
- <span data-ttu-id="6d68c-982">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-982">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-983">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-983">'Razor'</span></span>
- <span data-ttu-id="6d68c-984">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-984">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-985">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-985">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-986">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-986">'Blazor'</span></span>
- <span data-ttu-id="6d68c-987">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-987">'Identity'</span></span>
- <span data-ttu-id="6d68c-988">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-988">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-989">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-989">'Razor'</span></span>
- <span data-ttu-id="6d68c-990">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-990">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-991">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-991">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-992">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-992">'Blazor'</span></span>
- <span data-ttu-id="6d68c-993">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-993">'Identity'</span></span>
- <span data-ttu-id="6d68c-994">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-994">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-995">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-995">'Razor'</span></span>
- <span data-ttu-id="6d68c-996">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-996">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-997">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-997">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-998">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-998">'Blazor'</span></span>
- <span data-ttu-id="6d68c-999">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-999">'Identity'</span></span>
- <span data-ttu-id="6d68c-1000">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1000">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1001">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1001">'Razor'</span></span>
- <span data-ttu-id="6d68c-1002">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1002">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1003">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1003">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1004">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1004">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1005">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1005">'Identity'</span></span>
- <span data-ttu-id="6d68c-1006">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1006">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1007">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1007">'Razor'</span></span>
- <span data-ttu-id="6d68c-1008">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1008">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-1009">------------------- | | Yolu QueryString 'e yeniden yazın |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1009">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="6d68c-1010">`/path?var1=abc&var2=123`| | Bölme sonunda eğik çizgi |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1010">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="6d68c-1011">`/path`| | Sondaki eğik çizgiyi zorla |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1011">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="6d68c-1012">`/path/`| | Belirli istekleri yeniden yazmayı önleyin | `^(.*)(?<!\.axd)$`ya da`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1012">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="6d68c-1013">Yes`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1013">Yes: `/resource.htm`</span></span><br><span data-ttu-id="6d68c-1014">Eşleşen`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1014">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="6d68c-1015">`/resource.axd`| | URL segmentlerini yeniden Düzenle |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1015">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="6d68c-1016">`path/3/2/1`| | URL segmentini değiştirme |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1016">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="6d68c-1017">Bu belgede, ASP.NET Core uygulamalarında URL yeniden yazma ara yazılımı kullanma yönergeleriyle birlikte URL yeniden yazma tanıtılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1017">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="6d68c-1018">URL yeniden yazma, istek URL 'Lerini bir veya daha fazla önceden tanımlanmış kurala göre değiştirme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1018">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="6d68c-1019">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmaması için kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1019">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="6d68c-1020">URL yeniden yazma işlemi birkaç senaryoda yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1020">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="6d68c-1021">Sunucu kaynaklarını geçici olarak veya kalıcı olarak taşıyın veya değiştirin ve bu kaynakların kararlı konum belirleyicilerinin bakımını yapın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1021">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="6d68c-1022">İstek işlemeyi farklı uygulamalar arasında veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1022">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="6d68c-1023">Gelen isteklerde URL segmentlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1023">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="6d68c-1024">Arama motoru Iyileştirmesi (SEO) için genel URL 'Leri iyileştirin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1024">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="6d68c-1025">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmeye yardımcı olmak için kolay genel URL 'Lerin kullanılmasına izin verme.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1025">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="6d68c-1026">Güvensiz istekleri güvenli uç noktalara yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1026">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="6d68c-1027">Bir dış sitenin varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı Hotlink 'i engelleyin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1027">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="6d68c-1028">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1028">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="6d68c-1029">Uygun yerlerde kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1029">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="6d68c-1030">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6d68c-1030">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="6d68c-1031">URL yeniden yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="6d68c-1031">URL redirect and URL rewrite</span></span>

<span data-ttu-id="6d68c-1032">*URL yeniden yönlendirme* ve *URL yeniden yazma* arasındaki ifade farkı daha hafif ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1032">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="6d68c-1033">ASP.NET Core URL yeniden yazma ara yazılımı her ikisine de ihtiyacı verebilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1033">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="6d68c-1034">*URL yeniden yönlendirme* , istemcinin, ilk olarak istenen istemciden farklı bir adresteki kaynağa erişmesi için bir istemci tarafı işlemi içerir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1034">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="6d68c-1035">Bu, sunucuya gidiş dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1035">This requires a round trip to the server.</span></span> <span data-ttu-id="6d68c-1036">İstemci kaynak için yeni bir istek yaptığında, istemciye döndürülen yeniden yönlendirme URL 'SI tarayıcının adres çubuğunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1036">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="6d68c-1037">`/resource`Öğesine *yönlendiriliyorsa* `/different-resource` sunucu, istemcinin, `/different-resource` yeniden yönlendirmenin geçici ya da kalıcı olduğunu belirten bir durum kodu ile kaynağı elde etmesi gerektiğini yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1037">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmeti uç noktası, sürüm 1 ' den (v1) sunucudaki sürüm 2 ' ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="6d68c-1043">İstekleri farklı bir URL 'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yeniden yönlendirmenin kalıcı mi yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1043">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="6d68c-1044">*301-taşınan kalıcı* durum kodu, kaynağın yeni, kalıcı bir URL olduğu ve istemciye, gelecekteki tüm isteklerin kaynak için tüm istekleri yeni URL 'yi kullanması gerektiğini bildirmek istediğinizde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1044">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="6d68c-1045">*İstemci, 301 durum kodu alındığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="6d68c-1045">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="6d68c-1046">*302-bulunan* durum kodu, yeniden yönlendirmenin geçici ya da genellikle değişikliğe tabi olduğu durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1046">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="6d68c-1047">302 durum kodu, istemcinin URL 'YI depolayamadığını ve gelecekte kullanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1047">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="6d68c-1048">Durum kodları hakkında daha fazla bilgi için bkz. [RFC 2616: durum kodu tanımları](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="6d68c-1048">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="6d68c-1049">*URL yeniden yazma* , istenen istemciden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1049">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="6d68c-1050">URL yeniden yazma, sunucuya gidiş dönüş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1050">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="6d68c-1051">Yeniden yazan URL istemciye döndürülmüyor ve tarayıcının adres çubuğunda görünmüyor.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1051">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="6d68c-1052">`/resource`Öğesine *rewritten* `/different-resource` geri dönerse, sunucu, kaynağını *dahili olarak* getirir ve döndürür `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1052">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="6d68c-1053">İstemci, yeniden yazan URL 'de kaynağı alabiliyor olsa da, istemci isteği yaptığında ve yanıtı aldığında kaynağın yeniden yazan URL 'de bulunduğunu bilgilendirmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1053">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Sunucu üzerinde sürüm 1 ' den (v1) sürüm 2 ' ye (v2) bir WebAPI hizmet uç noktası değiştirilmiştir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="6d68c-1058">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="6d68c-1058">URL rewriting sample app</span></span>

<span data-ttu-id="6d68c-1059">[Örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)birlikte YENIDEN yazma URL 'sinin özelliklerini inceleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1059">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="6d68c-1060">Uygulama yeniden yönlendirme ve yeniden yazma kuralları uygular ve birkaç senaryo için yeniden yönlendirilen veya yeniden yazan URL 'YI gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1060">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="6d68c-1061">URL yeniden yazma ara yazılımı ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="6d68c-1061">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="6d68c-1062">Aşağıdaki yaklaşımlardan birini kullandığımmdan URL yeniden yazma ara yazılımı kullanın:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1062">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="6d68c-1063">Windows Server 'da IIS ile URL yeniden yazma modülü</span><span class="sxs-lookup"><span data-stu-id="6d68c-1063">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="6d68c-1064">Apache Server 'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="6d68c-1064">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="6d68c-1065">NGINX üzerinde URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="6d68c-1065">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="6d68c-1066">Ayrıca, uygulama [http. sys sunucusunda](xref:fundamentals/servers/httpsys) barındırıldığı zaman ara yazılımı kullanın (eski adıyla webListener olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="6d68c-1066">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="6d68c-1067">IIS, Apache ve NGINX 'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1067">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="6d68c-1068">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1068">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="6d68c-1069">Sunucu modüllerinin bazı özellikleri, `IsFile` `IsDirectory` IIS yeniden yazma modülünün ve kısıtlamaları gibi ASP.NET Core projelerle birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1069">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="6d68c-1070">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1070">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="6d68c-1071">Ara yazılım performansı büyük olasılıkla modüllerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1071">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="6d68c-1072">Sınama, performansı en iyi şekilde düşürür veya performans düşüklüğü göz ardı edilebilir olduğundan emin olmanın tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1072">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="6d68c-1073">Paket</span><span class="sxs-lookup"><span data-stu-id="6d68c-1073">Package</span></span>

<span data-ttu-id="6d68c-1074">Ara yazılımı projenize dahil etmek için, [Microsoft. aspnetcore. yeniden yazma](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketini içeren proje dosyasındaki [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app) öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1074">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="6d68c-1075">`Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız, pakete bir proje başvurusu ekleyin `Microsoft.AspNetCore.Rewrite` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1075">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="6d68c-1076">Uzantı ve Seçenekler</span><span class="sxs-lookup"><span data-stu-id="6d68c-1076">Extension and options</span></span>

<span data-ttu-id="6d68c-1077">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriyle [Rewriteoptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir ÖRNEĞINI oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1077">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="6d68c-1078">Birden çok kuralı, işlenmeyi istediğiniz sırada zincirle.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1078">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="6d68c-1079">, `RewriteOptions` İle istek ardışık düzenine eklendikçe, bu URL 'ye yeniden yazma ara yazılımı ile geçirilir <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="6d68c-1079">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="6d68c-1080">Www olmayan www 'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-1080">Redirect non-www to www</span></span>

<span data-ttu-id="6d68c-1081">Üç seçenek, uygulamanın istek olmayan istekleri yeniden yönlendirmesine izin verir `www` `www` :</span><span class="sxs-lookup"><span data-stu-id="6d68c-1081">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="6d68c-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: İstek değilse, istek alt etki alanına kalıcı olarak yeniden yönlendirin `www` `www` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1082"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="6d68c-1083">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1083">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="6d68c-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: `www` Gelen istek değilse isteği alt etki alanına yeniden yönlendirin `www` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1084"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="6d68c-1085">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1085">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="6d68c-1086">Aşırı yükleme, yanıt için durum kodu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1086">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="6d68c-1087"><xref:Microsoft.AspNetCore.Http.StatusCodes>Bir durum kodu ataması için sınıfın bir alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1087">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="6d68c-1088">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-1088">URL redirect</span></span>

<span data-ttu-id="6d68c-1089"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>İstekleri yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1089">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="6d68c-1090">İlk parametre gelen URL 'nin yolu ile eşleşen Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1090">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="6d68c-1091">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1091">The second parameter is the replacement string.</span></span> <span data-ttu-id="6d68c-1092">Varsa, üçüncü parametre durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1092">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="6d68c-1093">Durum kodunu belirtmezseniz, durum kodu varsayılan olarak *302-bulunur*; bu da kaynağın geçici olarak taşındığını veya değiştirildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1093">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="6d68c-1094">Geliştirici araçları etkinleştirilmiş bir tarayıcıda, örnek uygulamaya yol ile bir istek oluşturun `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1094">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="6d68c-1095">Regex, üzerindeki istek yoluyla eşleşir `redirect-rule/(.*)` ve yol ile değiştirilmiştir `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1095">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="6d68c-1096">Yeniden yönlendirme URL 'SI, *302 tarafından bulunan* bir durum kodu ile istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1096">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="6d68c-1097">Tarayıcı, tarayıcının adres çubuğunda görüntülenen yeniden yönlendirme URL 'SI üzerinde yeni bir istek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1097">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="6d68c-1098">Örnek uygulamadaki hiçbir kural, yeniden yönlendirme URL 'SI üzerinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1098">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="6d68c-1099">İkinci istek uygulamadan *200-Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1099">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="6d68c-1100">Yanıtın gövdesi, yeniden yönlendirme URL 'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1100">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="6d68c-1101">Bir URL *yeniden yönlendirildiğinde*sunucuya gidiş dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1101">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="6d68c-1102">Yeniden yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1102">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="6d68c-1103">Yeniden yönlendirme kuralları, bir yeniden yönlendirmeden sonra dahil olmak üzere, uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1103">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="6d68c-1104">Yanlışlıkla *sonsuz yeniden yönlendirmeler döngüsü*oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1104">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="6d68c-1105">Özgün Istek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1105">Original Request: `/redirect-rule/1234/5678`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="6d68c-1107">Parantez içinde yer alan ifadenin kısmına bir *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1107">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="6d68c-1108">İfadenin nokta ( `.` ), *herhangi bir karakterle eşleşir*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1108">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="6d68c-1109">Yıldız işareti ( `*` ) *, önceki karakterle sıfır veya daha fazla kez eşleşme*gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1109">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="6d68c-1110">Bu nedenle, URL 'nin son iki yol kesimi, `1234/5678` yakalama grubu tarafından yakalanır `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1110">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="6d68c-1111">Sonrasında istek URL 'sinde sağladığınız herhangi bir değer `redirect-rule/` Bu tek yakalama grubu tarafından yakalandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1111">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="6d68c-1112">Değiştirme dizesinde, yakalanan gruplar, dolar işareti ( `$` ) ve ardından yakalamanın sıra numarası ile birlikte dizeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1112">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="6d68c-1113">İlk yakalama grubu değeri ile elde edilir `$1` , ikincisi ile `$2` ve normal Regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1113">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="6d68c-1114">Örnek uygulamadaki yeniden yönlendirme kuralı Regex bölümünde yalnızca bir tane yakalanan grup bulunur, bu nedenle değiştirme dizesinde yalnızca bir tane eklenmiş grup vardır `$1` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1114">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="6d68c-1115">Kural uygulandığında, URL olur `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1115">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="6d68c-1116">Güvenli bir uç noktaya URL yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="6d68c-1116">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="6d68c-1117"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>Http ISTEKLERINI https protokolünü kullanarak aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1117">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="6d68c-1118">Durum kodu sağlanmazsa, ara yazılım varsayılan olarak *302-bulunur*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1118">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="6d68c-1119">Bağlantı noktası sağlanmazsa:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1119">If the port isn't supplied:</span></span>

* <span data-ttu-id="6d68c-1120">Ara yazılım varsayılan olarak olur `null` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1120">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="6d68c-1121">Şema `https` (https Protokolü) olarak değişir ve istemci, 443 numaralı bağlantı noktasında kaynağa erişir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1121">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="6d68c-1122">Aşağıdaki örnek, durum kodunun *301-kalıcı olarak taşınacağını* ve bağlantı noktasını 5001 olarak nasıl değiştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1122">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="6d68c-1123"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>Güvenli olmayan istekleri, bağlantı noktası 443 üzerinde GÜVENLI https Protokolü ile aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1123">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="6d68c-1124">Ara yazılım durum kodunu 301 olarak ayarlar ve *kalıcı olarak taşınır*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1124">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="6d68c-1125">Ek yeniden yönlendirme kuralları gereksinimi olmadan güvenli bir uç noktaya yönlendirilirken, HTTPS yeniden yönlendirme ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1125">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="6d68c-1126">Daha fazla bilgi için bkz. [https 'Yi zorla](xref:security/enforcing-ssl#require-https) konusu.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1126">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="6d68c-1127">Örnek uygulama, veya kullanımını gösterme yeteneğine sahiptir `AddRedirectToHttps` `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1127">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="6d68c-1128">Uzantı yöntemini öğesine ekleyin `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1128">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="6d68c-1129">Herhangi bir URL 'de uygulamaya güvenli olmayan bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1129">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="6d68c-1130">Otomatik olarak imzalanan sertifikanın güvenilmeyen tarayıcı güvenlik uyarısını kapatın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1130">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="6d68c-1131">Kullanarak özgün Istek `AddRedirectToHttps(301, 5001)` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1131">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="6d68c-1133">Kullanarak özgün Istek `AddRedirectToHttpsPermanent` :`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1133">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="6d68c-1135">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="6d68c-1135">URL rewrite</span></span>

<span data-ttu-id="6d68c-1136"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>URL 'leri yeniden yazma kuralı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1136">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="6d68c-1137">İlk parametre gelen URL yolundaki eşleşme için Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1137">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="6d68c-1138">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1138">The second parameter is the replacement string.</span></span> <span data-ttu-id="6d68c-1139">Üçüncü parametresi, `skipRemainingRules: {true|false}` geçerli kural uygulanmışsa ek yeniden yazma kurallarının atlanıp atlanmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1139">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="6d68c-1140">Özgün Istek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1140">Original Request: `/rewrite-rule/1234/5678`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="6d68c-1142">İfadenin başındaki simgeyi seçtiğinizde ( `^` ), eşleşmesinin URL yolunun başlangıcında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1142">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="6d68c-1143">Önceki örnekte, yeniden yönlendirme kuralıyla, Regex başlangıcında `redirect-rule/(.*)` simgeyi seçtiğinizde ( `^` ) yoktur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1143">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="6d68c-1144">Bu nedenle, `redirect-rule/` başarılı bir eşleşme için herhangi bir karakterden önce yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1144">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="6d68c-1145">Yol</span><span class="sxs-lookup"><span data-stu-id="6d68c-1145">Path</span></span>                               | <span data-ttu-id="6d68c-1146">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-1146">Match</span></span> |
| ---
<span data-ttu-id="6d68c-1147">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1147">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1148">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1148">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1149">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1149">'Identity'</span></span>
- <span data-ttu-id="6d68c-1150">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1150">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1151">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1151">'Razor'</span></span>
- <span data-ttu-id="6d68c-1152">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1152">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1153">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1153">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1154">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1154">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1155">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1155">'Identity'</span></span>
- <span data-ttu-id="6d68c-1156">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1156">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1157">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1157">'Razor'</span></span>
- <span data-ttu-id="6d68c-1158">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1158">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1159">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1159">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1160">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1160">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1161">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1161">'Identity'</span></span>
- <span data-ttu-id="6d68c-1162">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1162">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1163">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1163">'Razor'</span></span>
- <span data-ttu-id="6d68c-1164">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1164">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1165">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1165">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1166">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1166">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1167">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1167">'Identity'</span></span>
- <span data-ttu-id="6d68c-1168">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1168">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1169">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1169">'Razor'</span></span>
- <span data-ttu-id="6d68c-1170">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1170">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1171">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1171">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1172">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1172">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1173">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1173">'Identity'</span></span>
- <span data-ttu-id="6d68c-1174">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1174">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1175">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1175">'Razor'</span></span>
- <span data-ttu-id="6d68c-1176">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1176">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1177">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1177">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1178">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1179">'Identity'</span></span>
- <span data-ttu-id="6d68c-1180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1180">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1181">'Razor'</span></span>
- <span data-ttu-id="6d68c-1182">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1183">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1183">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1184">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1184">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1185">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1185">'Identity'</span></span>
- <span data-ttu-id="6d68c-1186">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1186">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1187">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1187">'Razor'</span></span>
- <span data-ttu-id="6d68c-1188">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1188">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1189">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1189">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1190">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1190">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1191">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1191">'Identity'</span></span>
- <span data-ttu-id="6d68c-1192">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1192">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1193">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1193">'Razor'</span></span>
- <span data-ttu-id="6d68c-1194">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1194">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1195">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1195">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1196">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1196">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1197">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1197">'Identity'</span></span>
- <span data-ttu-id="6d68c-1198">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1198">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1199">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1199">'Razor'</span></span>
- <span data-ttu-id="6d68c-1200">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1200">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1201">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1201">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1202">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1202">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1203">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1203">'Identity'</span></span>
- <span data-ttu-id="6d68c-1204">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1204">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1205">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1205">'Razor'</span></span>
- <span data-ttu-id="6d68c-1206">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1206">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1207">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1207">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1208">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1208">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1209">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1209">'Identity'</span></span>
- <span data-ttu-id="6d68c-1210">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1210">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1211">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1211">'Razor'</span></span>
- <span data-ttu-id="6d68c-1212">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1212">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1213">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1213">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1214">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1214">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1215">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1215">'Identity'</span></span>
- <span data-ttu-id="6d68c-1216">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1216">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1217">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1217">'Razor'</span></span>
- <span data-ttu-id="6d68c-1218">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1218">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1219">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1219">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1220">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1221">'Identity'</span></span>
- <span data-ttu-id="6d68c-1222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1222">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1223">'Razor'</span></span>
- <span data-ttu-id="6d68c-1224">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1225">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1225">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1226">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1226">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1227">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1227">'Identity'</span></span>
- <span data-ttu-id="6d68c-1228">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1228">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1229">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1229">'Razor'</span></span>
- <span data-ttu-id="6d68c-1230">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1230">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1231">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1231">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1232">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1232">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1233">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1233">'Identity'</span></span>
- <span data-ttu-id="6d68c-1234">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1234">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1235">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1235">'Razor'</span></span>
- <span data-ttu-id="6d68c-1236">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1236">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Evet | | `/my-cool-redirect-rule/1234/5678` | Evet | | `/anotherredirect-rule/1234/5678`  | Evet |</span><span class="sxs-lookup"><span data-stu-id="6d68c-1237">----------------- | :---: | | `/redirect-rule/1234/5678`         | Yes   | | `/my-cool-redirect-rule/1234/5678` | Yes   | | `/anotherredirect-rule/1234/5678`  | Yes   |</span></span>

<span data-ttu-id="6d68c-1238">Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)` yalnızca ile başlarsa yollarla eşleşir `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1238">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="6d68c-1239">Aşağıdaki tabloda, eşleşen farkı aklınızda.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1239">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="6d68c-1240">Yol</span><span class="sxs-lookup"><span data-stu-id="6d68c-1240">Path</span></span>                              | <span data-ttu-id="6d68c-1241">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="6d68c-1241">Match</span></span> |
| ---
<span data-ttu-id="6d68c-1242">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1242">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1243">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1244">'Identity'</span></span>
- <span data-ttu-id="6d68c-1245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1245">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1246">'Razor'</span></span>
- <span data-ttu-id="6d68c-1247">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1248">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1248">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1249">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1250">'Identity'</span></span>
- <span data-ttu-id="6d68c-1251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1251">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1252">'Razor'</span></span>
- <span data-ttu-id="6d68c-1253">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1254">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1255">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1256">'Identity'</span></span>
- <span data-ttu-id="6d68c-1257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1257">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1258">'Razor'</span></span>
- <span data-ttu-id="6d68c-1259">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1260">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1260">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1261">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1262">'Identity'</span></span>
- <span data-ttu-id="6d68c-1263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1263">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1264">'Razor'</span></span>
- <span data-ttu-id="6d68c-1265">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1266">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1267">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1268">'Identity'</span></span>
- <span data-ttu-id="6d68c-1269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1269">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1270">'Razor'</span></span>
- <span data-ttu-id="6d68c-1271">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1272">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1273">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1274">'Identity'</span></span>
- <span data-ttu-id="6d68c-1275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1275">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1276">'Razor'</span></span>
- <span data-ttu-id="6d68c-1277">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1278">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1278">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1279">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1280">'Identity'</span></span>
- <span data-ttu-id="6d68c-1281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1281">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1282">'Razor'</span></span>
- <span data-ttu-id="6d68c-1283">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1284">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1284">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1285">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1286">'Identity'</span></span>
- <span data-ttu-id="6d68c-1287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1287">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1288">'Razor'</span></span>
- <span data-ttu-id="6d68c-1289">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1290">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1290">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1291">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1292">'Identity'</span></span>
- <span data-ttu-id="6d68c-1293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1293">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1294">'Razor'</span></span>
- <span data-ttu-id="6d68c-1295">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1296">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1296">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1297">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1298">'Identity'</span></span>
- <span data-ttu-id="6d68c-1299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1299">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1300">'Razor'</span></span>
- <span data-ttu-id="6d68c-1301">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1302">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1302">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1303">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1304">'Identity'</span></span>
- <span data-ttu-id="6d68c-1305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1305">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1306">'Razor'</span></span>
- <span data-ttu-id="6d68c-1307">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1308">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1308">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1309">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1310">'Identity'</span></span>
- <span data-ttu-id="6d68c-1311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1311">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1312">'Razor'</span></span>
- <span data-ttu-id="6d68c-1313">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1314">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1314">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1315">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1316">'Identity'</span></span>
- <span data-ttu-id="6d68c-1317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1317">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1318">'Razor'</span></span>
- <span data-ttu-id="6d68c-1319">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1320">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1320">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1321">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1322">'Identity'</span></span>
- <span data-ttu-id="6d68c-1323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1323">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1324">'Razor'</span></span>
- <span data-ttu-id="6d68c-1325">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1325">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Evet | | `/my-cool-rewrite-rule/1234/5678` | Hayır | | `/anotherrewrite-rule/1234/5678`  | Hayır |</span><span class="sxs-lookup"><span data-stu-id="6d68c-1326">----------------- | :---: | | `/rewrite-rule/1234/5678`         | Yes   | | `/my-cool-rewrite-rule/1234/5678` | No    | | `/anotherrewrite-rule/1234/5678`  | No    |</span></span>

<span data-ttu-id="6d68c-1327">`^rewrite-rule/`İfadenin bölümünü takip eden iki yakalama grubu vardır `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1327">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="6d68c-1328">`\d`Belirtir *bir sayıyla (sayı) eşleşir*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1328">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="6d68c-1329">Artı işareti ( `+` ) *bir veya daha fazla önceki karakterden eşleşiyor*demektir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1329">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="6d68c-1330">Bu nedenle, URL bir sayı içermeli ve ardından İleri eğik çizgi ve ardından başka bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1330">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="6d68c-1331">Bu yakalama grupları, ve olarak yeniden yazan URL 'sine `$1` eklenir `$2` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1331">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="6d68c-1332">Yeniden yazma kuralı değiştirme dizesi yakalanan grupları sorgu dizesine koyar.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1332">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="6d68c-1333">İstenen yolu, `/rewrite-rule/1234/5678` kaynağı elde etmek için yeniden yazılır `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1333">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="6d68c-1334">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazdığınızda korunur.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1334">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="6d68c-1335">Kaynağı almak için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1335">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="6d68c-1336">Kaynak varsa, bu, alınır ve istemciye *200-ok* durum kodu ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1336">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="6d68c-1337">İstemci yeniden yönlendirmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1337">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="6d68c-1338">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamaz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1338">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="6d68c-1339">`skipRemainingRules: true`Eşleşen kuralların hesaplama maliyeti ve uygulama yanıt süresini arttığı için mümkün olan her durumda kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1339">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="6d68c-1340">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1340">For the fastest app response:</span></span>
>
> * <span data-ttu-id="6d68c-1341">En sık eşleşen kuraldan en az sıklıkta eşleşen kurala göre yeniden yazma kuralları.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1341">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="6d68c-1342">Bir eşleşme gerçekleştiğinde ve ek kural işleme gerekli olmadığında kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1342">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="6d68c-1343">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="6d68c-1343">Apache mod_rewrite</span></span>

<span data-ttu-id="6d68c-1344">İle Apache mod_rewrite kuralları uygulayın <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1344">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="6d68c-1345">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1345">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="6d68c-1346">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için bkz. [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="6d68c-1346">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="6d68c-1347">, <xref:System.IO.StreamReader> *ApacheModRewrite. txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1347">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="6d68c-1348">Örnek uygulama, istekleri ' den ' e yeniden yönlendirir `/apache-mod-rules-redirect/(.\*)` `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1348">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="6d68c-1349">Yanıt durum kodu *302-bulundu*.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1349">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="6d68c-1350">Özgün Istek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1350">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="6d68c-1352">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1352">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="6d68c-1353">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-1353">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="6d68c-1354">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="6d68c-1354">HTTP_ACCEPT</span></span>
* <span data-ttu-id="6d68c-1355">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="6d68c-1355">HTTP_CONNECTION</span></span>
* <span data-ttu-id="6d68c-1356">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="6d68c-1356">HTTP_COOKIE</span></span>
* <span data-ttu-id="6d68c-1357">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="6d68c-1357">HTTP_FORWARDED</span></span>
* <span data-ttu-id="6d68c-1358">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="6d68c-1358">HTTP_HOST</span></span>
* <span data-ttu-id="6d68c-1359">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="6d68c-1359">HTTP_REFERER</span></span>
* <span data-ttu-id="6d68c-1360">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="6d68c-1360">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="6d68c-1361">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6d68c-1361">HTTPS</span></span>
* <span data-ttu-id="6d68c-1362">IPV6</span><span class="sxs-lookup"><span data-stu-id="6d68c-1362">IPV6</span></span>
* <span data-ttu-id="6d68c-1363">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="6d68c-1363">QUERY_STRING</span></span>
* <span data-ttu-id="6d68c-1364">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-1364">REMOTE_ADDR</span></span>
* <span data-ttu-id="6d68c-1365">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="6d68c-1365">REMOTE_PORT</span></span>
* <span data-ttu-id="6d68c-1366">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="6d68c-1366">REQUEST_FILENAME</span></span>
* <span data-ttu-id="6d68c-1367">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="6d68c-1367">REQUEST_METHOD</span></span>
* <span data-ttu-id="6d68c-1368">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="6d68c-1368">REQUEST_SCHEME</span></span>
* <span data-ttu-id="6d68c-1369">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="6d68c-1369">REQUEST_URI</span></span>
* <span data-ttu-id="6d68c-1370">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="6d68c-1370">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="6d68c-1371">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-1371">SERVER_ADDR</span></span>
* <span data-ttu-id="6d68c-1372">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="6d68c-1372">SERVER_PORT</span></span>
* <span data-ttu-id="6d68c-1373">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="6d68c-1373">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="6d68c-1374">TIME</span><span class="sxs-lookup"><span data-stu-id="6d68c-1374">TIME</span></span>
* <span data-ttu-id="6d68c-1375">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="6d68c-1375">TIME_DAY</span></span>
* <span data-ttu-id="6d68c-1376">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="6d68c-1376">TIME_HOUR</span></span>
* <span data-ttu-id="6d68c-1377">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="6d68c-1377">TIME_MIN</span></span>
* <span data-ttu-id="6d68c-1378">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="6d68c-1378">TIME_MON</span></span>
* <span data-ttu-id="6d68c-1379">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="6d68c-1379">TIME_SEC</span></span>
* <span data-ttu-id="6d68c-1380">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="6d68c-1380">TIME_WDAY</span></span>
* <span data-ttu-id="6d68c-1381">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="6d68c-1381">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="6d68c-1382">IIS URL yeniden yazma modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="6d68c-1382">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="6d68c-1383">IIS URL yeniden yazma modülü için geçerli olan kural kümesini kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1383">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="6d68c-1384">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1384">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="6d68c-1385">Windows Server IIS 'de çalışırken uygulamanın *Web. config* dosyasını kullanmak için ara yazılımı yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1385">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="6d68c-1386">IIS ile, IIS yeniden yazma modülüyle çakışmalardan kaçınmak için bu kuralların uygulamanın *Web. config* dosyası dışında depolanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1386">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="6d68c-1387">Daha fazla bilgi ve IIS URL yeniden yazma modülü kurallarının örnekleri için bkz. [Using URL yeniden yazma modülü 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) ve [URL yeniden yazma modülü yapılandırma başvurusu](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="6d68c-1387">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="6d68c-1388">, <xref:System.IO.StreamReader> *Iisurlyeniden yazma. xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1388">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="6d68c-1389">Örnek uygulama, ' den ' e olan istekleri yeniden yazar `/iis-rules-rewrite/(.*)` `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1389">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="6d68c-1390">Yanıt, istemciye *200-ok* durum kodu ile gönderilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1390">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="6d68c-1391">Özgün Istek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1391">Original Request: `/iis-rules-rewrite/1234`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="6d68c-1393">Uygulamanızı istenmeyen yollarla etkileyebilecek sunucu düzeyi kurallara sahip etkin bir IIS yeniden yazma modülünüzün olması halinde, bir uygulama için IIS yeniden yazma modülünü devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1393">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="6d68c-1394">Daha fazla bilgi için bkz. [IIS modüllerini devre dışı bırakma](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="6d68c-1394">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="6d68c-1395">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="6d68c-1395">Unsupported features</span></span>

<span data-ttu-id="6d68c-1396">ASP.NET Core 2. x ile yayınlanan ara yazılım, aşağıdaki IIS URL yeniden yazma modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1396">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="6d68c-1397">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="6d68c-1397">Outbound Rules</span></span>
* <span data-ttu-id="6d68c-1398">Özel sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="6d68c-1398">Custom Server Variables</span></span>
* <span data-ttu-id="6d68c-1399">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="6d68c-1399">Wildcards</span></span>
* <span data-ttu-id="6d68c-1400">LogRewrittenUrl 'Si</span><span class="sxs-lookup"><span data-stu-id="6d68c-1400">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="6d68c-1401">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="6d68c-1401">Supported server variables</span></span>

<span data-ttu-id="6d68c-1402">Ara yazılım aşağıdaki IIS URL yeniden yazma modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1402">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="6d68c-1403">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="6d68c-1403">CONTENT_LENGTH</span></span>
* <span data-ttu-id="6d68c-1404">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="6d68c-1404">CONTENT_TYPE</span></span>
* <span data-ttu-id="6d68c-1405">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="6d68c-1405">HTTP_ACCEPT</span></span>
* <span data-ttu-id="6d68c-1406">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="6d68c-1406">HTTP_CONNECTION</span></span>
* <span data-ttu-id="6d68c-1407">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="6d68c-1407">HTTP_COOKIE</span></span>
* <span data-ttu-id="6d68c-1408">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="6d68c-1408">HTTP_HOST</span></span>
* <span data-ttu-id="6d68c-1409">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="6d68c-1409">HTTP_REFERER</span></span>
* <span data-ttu-id="6d68c-1410">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="6d68c-1410">HTTP_URL</span></span>
* <span data-ttu-id="6d68c-1411">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="6d68c-1411">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="6d68c-1412">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6d68c-1412">HTTPS</span></span>
* <span data-ttu-id="6d68c-1413">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-1413">LOCAL_ADDR</span></span>
* <span data-ttu-id="6d68c-1414">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="6d68c-1414">QUERY_STRING</span></span>
* <span data-ttu-id="6d68c-1415">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="6d68c-1415">REMOTE_ADDR</span></span>
* <span data-ttu-id="6d68c-1416">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="6d68c-1416">REMOTE_PORT</span></span>
* <span data-ttu-id="6d68c-1417">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="6d68c-1417">REQUEST_FILENAME</span></span>
* <span data-ttu-id="6d68c-1418">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="6d68c-1418">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="6d68c-1419">Ayrıca bir ile elde edebilirsiniz <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1419">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="6d68c-1420">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1420">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="6d68c-1421">Yeniden yazma kuralları dosyalarınızın sağladığınız yoldaki sunucuya dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1421">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="6d68c-1422">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="6d68c-1422">Method-based rule</span></span>

<span data-ttu-id="6d68c-1423"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Bir yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1423">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="6d68c-1424">`Add`, <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> metodunda kullanım için kullanılabilir hale getiren öğesini gösterir <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1424">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="6d68c-1425">[Rewritecontext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) , ek ardışık düzen işlemenin nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1425">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="6d68c-1426">Değeri <xref:Microsoft.AspNetCore.Rewrite.RuleResult> Aşağıdaki tabloda açıklanan alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1426">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="6d68c-1427">Eylem</span><span class="sxs-lookup"><span data-stu-id="6d68c-1427">Action</span></span>                                                           |
| ---
<span data-ttu-id="6d68c-1428">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1428">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1429">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1430">'Identity'</span></span>
- <span data-ttu-id="6d68c-1431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1431">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1432">'Razor'</span></span>
- <span data-ttu-id="6d68c-1433">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1433">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1434">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1434">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1435">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1435">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1436">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1436">'Identity'</span></span>
- <span data-ttu-id="6d68c-1437">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1437">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1438">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1438">'Razor'</span></span>
- <span data-ttu-id="6d68c-1439">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1439">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1440">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1440">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1441">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1441">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1442">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1442">'Identity'</span></span>
- <span data-ttu-id="6d68c-1443">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1443">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1444">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1444">'Razor'</span></span>
- <span data-ttu-id="6d68c-1445">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1445">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1446">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1446">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1447">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1447">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1448">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1448">'Identity'</span></span>
- <span data-ttu-id="6d68c-1449">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1449">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1450">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1450">'Razor'</span></span>
- <span data-ttu-id="6d68c-1451">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1451">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1452">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1452">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1453">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1453">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1454">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1454">'Identity'</span></span>
- <span data-ttu-id="6d68c-1455">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1455">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1456">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1456">'Razor'</span></span>
- <span data-ttu-id="6d68c-1457">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1457">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1458">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1458">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1459">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1459">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1460">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1460">'Identity'</span></span>
- <span data-ttu-id="6d68c-1461">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1461">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1462">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1462">'Razor'</span></span>
- <span data-ttu-id="6d68c-1463">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1463">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1464">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1464">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1465">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1466">'Identity'</span></span>
- <span data-ttu-id="6d68c-1467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1467">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1468">'Razor'</span></span>
- <span data-ttu-id="6d68c-1469">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1470">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1470">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1471">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1472">'Identity'</span></span>
- <span data-ttu-id="6d68c-1473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1473">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1474">'Razor'</span></span>
- <span data-ttu-id="6d68c-1475">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1476">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1476">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1477">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1477">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1478">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1478">'Identity'</span></span>
- <span data-ttu-id="6d68c-1479">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1479">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1480">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1480">'Razor'</span></span>
- <span data-ttu-id="6d68c-1481">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1481">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1482">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1482">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1483">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1483">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1484">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1484">'Identity'</span></span>
- <span data-ttu-id="6d68c-1485">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1485">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1486">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1486">'Razor'</span></span>
- <span data-ttu-id="6d68c-1487">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1487">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1488">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1488">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1489">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1489">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1490">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1490">'Identity'</span></span>
- <span data-ttu-id="6d68c-1491">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1491">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1492">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1492">'Razor'</span></span>
- <span data-ttu-id="6d68c-1493">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1493">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1494">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1494">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1495">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1495">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1496">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1496">'Identity'</span></span>
- <span data-ttu-id="6d68c-1497">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1497">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1498">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1498">'Razor'</span></span>
- <span data-ttu-id="6d68c-1499">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1499">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1500">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1500">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1501">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1501">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1502">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1502">'Identity'</span></span>
- <span data-ttu-id="6d68c-1503">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1503">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1504">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1504">'Razor'</span></span>
- <span data-ttu-id="6d68c-1505">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1505">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1506">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1506">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1507">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1507">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1508">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1508">'Identity'</span></span>
- <span data-ttu-id="6d68c-1509">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1509">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1510">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1510">'Razor'</span></span>
- <span data-ttu-id="6d68c-1511">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1511">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1512">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1512">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1513">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1513">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1514">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1514">'Identity'</span></span>
- <span data-ttu-id="6d68c-1515">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1515">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1516">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1516">'Razor'</span></span>
- <span data-ttu-id="6d68c-1517">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1517">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1518">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1518">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1519">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1519">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1520">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1520">'Identity'</span></span>
- <span data-ttu-id="6d68c-1521">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1521">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1522">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1522">'Razor'</span></span>
- <span data-ttu-id="6d68c-1523">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1523">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-1524">------------------ | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1524">------------------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1525">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1525">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1526">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1526">'Identity'</span></span>
- <span data-ttu-id="6d68c-1527">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1527">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1528">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1528">'Razor'</span></span>
- <span data-ttu-id="6d68c-1529">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1529">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1530">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1530">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1531">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1532">'Identity'</span></span>
- <span data-ttu-id="6d68c-1533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1533">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1534">'Razor'</span></span>
- <span data-ttu-id="6d68c-1535">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1536">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1536">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1537">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1537">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1538">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1538">'Identity'</span></span>
- <span data-ttu-id="6d68c-1539">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1539">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1540">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1540">'Razor'</span></span>
- <span data-ttu-id="6d68c-1541">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1541">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1542">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1542">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1543">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1543">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1544">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1544">'Identity'</span></span>
- <span data-ttu-id="6d68c-1545">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1545">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1546">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1546">'Razor'</span></span>
- <span data-ttu-id="6d68c-1547">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1547">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1548">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1548">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1549">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1549">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1550">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1550">'Identity'</span></span>
- <span data-ttu-id="6d68c-1551">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1551">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1552">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1552">'Razor'</span></span>
- <span data-ttu-id="6d68c-1553">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1553">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1554">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1554">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1555">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1555">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1556">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1556">'Identity'</span></span>
- <span data-ttu-id="6d68c-1557">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1557">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1558">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1558">'Razor'</span></span>
- <span data-ttu-id="6d68c-1559">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1559">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1560">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1560">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1561">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1561">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1562">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1562">'Identity'</span></span>
- <span data-ttu-id="6d68c-1563">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1563">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1564">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1564">'Razor'</span></span>
- <span data-ttu-id="6d68c-1565">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1565">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1566">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1566">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1567">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1567">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1568">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1568">'Identity'</span></span>
- <span data-ttu-id="6d68c-1569">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1569">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1570">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1570">'Razor'</span></span>
- <span data-ttu-id="6d68c-1571">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1571">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1572">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1572">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1573">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1574">'Identity'</span></span>
- <span data-ttu-id="6d68c-1575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1575">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1576">'Razor'</span></span>
- <span data-ttu-id="6d68c-1577">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1578">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1578">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1579">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1579">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1580">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1580">'Identity'</span></span>
- <span data-ttu-id="6d68c-1581">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1581">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1582">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1582">'Razor'</span></span>
- <span data-ttu-id="6d68c-1583">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1583">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1584">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1584">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1585">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1585">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1586">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1586">'Identity'</span></span>
- <span data-ttu-id="6d68c-1587">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1587">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1588">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1588">'Razor'</span></span>
- <span data-ttu-id="6d68c-1589">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1589">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1590">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1590">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1591">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1591">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1592">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1592">'Identity'</span></span>
- <span data-ttu-id="6d68c-1593">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1593">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1594">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1594">'Razor'</span></span>
- <span data-ttu-id="6d68c-1595">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1595">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1596">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1596">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1597">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1597">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1598">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1598">'Identity'</span></span>
- <span data-ttu-id="6d68c-1599">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1599">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1600">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1600">'Razor'</span></span>
- <span data-ttu-id="6d68c-1601">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1601">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1602">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1602">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1603">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1603">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1604">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1604">'Identity'</span></span>
- <span data-ttu-id="6d68c-1605">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1605">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1606">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1606">'Razor'</span></span>
- <span data-ttu-id="6d68c-1607">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1607">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1608">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1608">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1609">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1609">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1610">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1610">'Identity'</span></span>
- <span data-ttu-id="6d68c-1611">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1611">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1612">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1612">'Razor'</span></span>
- <span data-ttu-id="6d68c-1613">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1613">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1614">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1614">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1615">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1616">'Identity'</span></span>
- <span data-ttu-id="6d68c-1617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1617">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1618">'Razor'</span></span>
- <span data-ttu-id="6d68c-1619">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1620">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1620">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1621">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1621">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1622">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1622">'Identity'</span></span>
- <span data-ttu-id="6d68c-1623">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1623">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1624">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1624">'Razor'</span></span>
- <span data-ttu-id="6d68c-1625">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1625">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1626">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1626">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1627">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1627">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1628">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1628">'Identity'</span></span>
- <span data-ttu-id="6d68c-1629">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1629">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1630">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1630">'Razor'</span></span>
- <span data-ttu-id="6d68c-1631">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1631">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1632">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1632">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1633">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1633">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1634">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1634">'Identity'</span></span>
- <span data-ttu-id="6d68c-1635">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1635">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1636">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1636">'Razor'</span></span>
- <span data-ttu-id="6d68c-1637">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1637">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1638">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1638">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1639">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1639">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1640">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1640">'Identity'</span></span>
- <span data-ttu-id="6d68c-1641">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1641">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1642">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1642">'Razor'</span></span>
- <span data-ttu-id="6d68c-1643">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1643">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1644">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1644">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1645">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1645">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1646">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1646">'Identity'</span></span>
- <span data-ttu-id="6d68c-1647">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1647">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1648">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1648">'Razor'</span></span>
- <span data-ttu-id="6d68c-1649">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1649">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1650">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1650">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1651">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1651">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1652">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1652">'Identity'</span></span>
- <span data-ttu-id="6d68c-1653">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1653">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1654">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1654">'Razor'</span></span>
- <span data-ttu-id="6d68c-1655">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1655">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1656">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1656">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1657">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1658">'Identity'</span></span>
- <span data-ttu-id="6d68c-1659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1659">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1660">'Razor'</span></span>
- <span data-ttu-id="6d68c-1661">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1662">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1662">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1663">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1663">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1664">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1664">'Identity'</span></span>
- <span data-ttu-id="6d68c-1665">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1665">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1666">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1666">'Razor'</span></span>
- <span data-ttu-id="6d68c-1667">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1667">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1668">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1668">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1669">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1669">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1670">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1670">'Identity'</span></span>
- <span data-ttu-id="6d68c-1671">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1671">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1672">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1672">'Razor'</span></span>
- <span data-ttu-id="6d68c-1673">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1673">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1674">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1674">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1675">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1675">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1676">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1676">'Identity'</span></span>
- <span data-ttu-id="6d68c-1677">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1677">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1678">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1678">'Razor'</span></span>
- <span data-ttu-id="6d68c-1679">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1679">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1680">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1680">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1681">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1681">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1682">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1682">'Identity'</span></span>
- <span data-ttu-id="6d68c-1683">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1683">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1684">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1684">'Razor'</span></span>
- <span data-ttu-id="6d68c-1685">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1685">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1686">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1686">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1687">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1687">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1688">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1688">'Identity'</span></span>
- <span data-ttu-id="6d68c-1689">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1689">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1690">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1690">'Razor'</span></span>
- <span data-ttu-id="6d68c-1691">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1691">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1692">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1692">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1693">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1693">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1694">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1694">'Identity'</span></span>
- <span data-ttu-id="6d68c-1695">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1695">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1696">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1696">'Razor'</span></span>
- <span data-ttu-id="6d68c-1697">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1697">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1698">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1698">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1699">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1699">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1700">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1700">'Identity'</span></span>
- <span data-ttu-id="6d68c-1701">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1701">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1702">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1702">'Razor'</span></span>
- <span data-ttu-id="6d68c-1703">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1703">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-1704">-------------------------------- | | `RuleResult.ContinueRules`(varsayılan) | Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1704">-------------------------------- | | `RuleResult.ContinueRules` (default) | Continue applying rules.</span></span>                                         <span data-ttu-id="6d68c-1705">| | `RuleResult.EndResponse`             | Kuralları uygulamayı durdurun ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1705">| | `RuleResult.EndResponse`             | Stop applying rules and send the response.</span></span>                       <span data-ttu-id="6d68c-1706">| | `RuleResult.SkipRemainingRules`      | Kuralları uygulamayı durdurun ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1706">| | `RuleResult.SkipRemainingRules`      | Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="6d68c-1707">Örnek uygulama, *. xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1707">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="6d68c-1708">İçin bir istek yapılırsa `/file.xml` , istek öğesine yeniden yönlendirilir `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1708">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="6d68c-1709">Durum kodu *301*olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1709">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="6d68c-1710">Tarayıcı */xmlfiles/File.xml*için yeni bir istek yaptığında, statik dosya ara yazılımı dosyayı *Wwwroot/xmlfiles* klasöründen istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1710">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="6d68c-1711">Yeniden yönlendirme için, yanıtın durum kodunu açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1711">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="6d68c-1712">Aksi takdirde, *200-ok* durum kodu döndürülür ve yeniden yönlendirme istemcide gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1712">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="6d68c-1713">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1713">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="6d68c-1714">Bu yaklaşım ayrıca istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1714">This approach can also rewrite requests.</span></span> <span data-ttu-id="6d68c-1715">Örnek uygulama, *dosya. txt* metin dosyasına *Wwwroot* klasöründen hizmeti sağlamak için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1715">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="6d68c-1716">Statik dosya ara yazılımı, güncelleştirilmiş istek yoluna göre dosyayı sunar:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1716">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="6d68c-1717">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1717">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="6d68c-1718">Irule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="6d68c-1718">IRule-based rule</span></span>

<span data-ttu-id="6d68c-1719"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Arabirimini uygulayan bir sınıfta kural mantığını kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IRule> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1719">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="6d68c-1720">`IRule`Yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1720">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="6d68c-1721">Uygulama sınıfınız, yöntemi için parametreleri geçirebilmeniz için bir Oluşturucu içerebilir <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1721">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="6d68c-1722">Ve için örnek uygulamadaki parametrelerin değerleri, `extension` `newPath` çeşitli koşullara uyacak şekilde denetlenir.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1722">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="6d68c-1723">`extension`Bir değer içermeli ve değer *. png*, *. jpg*veya *. gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1723">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="6d68c-1724">`newPath`Geçerli değilse, bir oluşturulur <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1724">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="6d68c-1725">*Image. png*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1725">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="6d68c-1726">*Image. jpg*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="6d68c-1726">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="6d68c-1727">Durum kodu 301 olarak ayarlanır ve *kalıcı olarak taşınır*ve `context.Result` kuralları işlemeyi durdur ve yanıtı gönder olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="6d68c-1727">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="6d68c-1728">Özgün Istek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1728">Original Request: `/image.png`</span></span>

![Görüntü. png için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="6d68c-1730">Özgün Istek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1730">Original Request: `/image.jpg`</span></span>

![Image. jpg için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="6d68c-1732">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="6d68c-1732">Regex examples</span></span>

| <span data-ttu-id="6d68c-1733">Hedef</span><span class="sxs-lookup"><span data-stu-id="6d68c-1733">Goal</span></span> | <span data-ttu-id="6d68c-1734">Regex dize &</span><span class="sxs-lookup"><span data-stu-id="6d68c-1734">Regex String &</span></span><br><span data-ttu-id="6d68c-1735">Match örneği</span><span class="sxs-lookup"><span data-stu-id="6d68c-1735">Match Example</span></span> | <span data-ttu-id="6d68c-1736">Değiştirme dizesi &</span><span class="sxs-lookup"><span data-stu-id="6d68c-1736">Replacement String &</span></span><br><span data-ttu-id="6d68c-1737">Çıkış örneği</span><span class="sxs-lookup"><span data-stu-id="6d68c-1737">Output Example</span></span> |
| ---- | ---
<span data-ttu-id="6d68c-1738">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1738">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1739">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1739">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1740">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1740">'Identity'</span></span>
- <span data-ttu-id="6d68c-1741">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1741">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1742">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1742">'Razor'</span></span>
- <span data-ttu-id="6d68c-1743">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1743">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1744">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1744">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1745">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1745">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1746">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1746">'Identity'</span></span>
- <span data-ttu-id="6d68c-1747">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1747">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1748">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1748">'Razor'</span></span>
- <span data-ttu-id="6d68c-1749">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1749">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1750">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1750">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1751">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1751">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1752">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1752">'Identity'</span></span>
- <span data-ttu-id="6d68c-1753">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1753">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1754">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1754">'Razor'</span></span>
- <span data-ttu-id="6d68c-1755">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1755">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1756">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1756">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1757">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1757">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1758">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1758">'Identity'</span></span>
- <span data-ttu-id="6d68c-1759">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1759">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1760">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1760">'Razor'</span></span>
- <span data-ttu-id="6d68c-1761">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1761">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1762">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1762">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1763">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1763">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1764">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1764">'Identity'</span></span>
- <span data-ttu-id="6d68c-1765">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1765">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1766">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1766">'Razor'</span></span>
- <span data-ttu-id="6d68c-1767">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1767">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1768">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1768">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1769">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1769">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1770">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1770">'Identity'</span></span>
- <span data-ttu-id="6d68c-1771">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1771">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1772">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1772">'Razor'</span></span>
- <span data-ttu-id="6d68c-1773">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1773">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1774">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1774">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1775">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1775">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1776">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1776">'Identity'</span></span>
- <span data-ttu-id="6d68c-1777">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1777">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1778">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1778">'Razor'</span></span>
- <span data-ttu-id="6d68c-1779">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1779">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1780">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1780">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1781">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1781">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1782">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1782">'Identity'</span></span>
- <span data-ttu-id="6d68c-1783">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1783">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1784">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1784">'Razor'</span></span>
- <span data-ttu-id="6d68c-1785">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1785">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1786">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1786">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1787">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1787">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1788">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1788">'Identity'</span></span>
- <span data-ttu-id="6d68c-1789">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1789">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1790">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1790">'Razor'</span></span>
- <span data-ttu-id="6d68c-1791">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1791">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1792">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1792">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1793">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1793">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1794">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1794">'Identity'</span></span>
- <span data-ttu-id="6d68c-1795">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1795">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1796">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1796">'Razor'</span></span>
- <span data-ttu-id="6d68c-1797">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1797">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1798">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1798">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1799">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1799">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1800">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1800">'Identity'</span></span>
- <span data-ttu-id="6d68c-1801">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1801">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1802">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1802">'Razor'</span></span>
- <span data-ttu-id="6d68c-1803">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1803">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1804">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1804">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1805">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1805">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1806">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1806">'Identity'</span></span>
- <span data-ttu-id="6d68c-1807">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1807">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1808">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1808">'Razor'</span></span>
- <span data-ttu-id="6d68c-1809">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1809">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1810">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1810">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1811">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1811">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1812">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1812">'Identity'</span></span>
- <span data-ttu-id="6d68c-1813">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1813">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1814">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1814">'Razor'</span></span>
- <span data-ttu-id="6d68c-1815">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1815">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-1816">---------------- | ---title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1816">---------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1817">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1817">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1818">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1818">'Identity'</span></span>
- <span data-ttu-id="6d68c-1819">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1819">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1820">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1820">'Razor'</span></span>
- <span data-ttu-id="6d68c-1821">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1821">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1822">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1822">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1823">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1823">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1824">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1824">'Identity'</span></span>
- <span data-ttu-id="6d68c-1825">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1825">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1826">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1826">'Razor'</span></span>
- <span data-ttu-id="6d68c-1827">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1827">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1828">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1828">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1829">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1829">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1830">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1830">'Identity'</span></span>
- <span data-ttu-id="6d68c-1831">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1831">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1832">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1832">'Razor'</span></span>
- <span data-ttu-id="6d68c-1833">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1833">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1834">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1834">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1835">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1835">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1836">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1836">'Identity'</span></span>
- <span data-ttu-id="6d68c-1837">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1837">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1838">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1838">'Razor'</span></span>
- <span data-ttu-id="6d68c-1839">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1839">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1840">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1840">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1841">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1841">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1842">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1842">'Identity'</span></span>
- <span data-ttu-id="6d68c-1843">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1843">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1844">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1844">'Razor'</span></span>
- <span data-ttu-id="6d68c-1845">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1845">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1846">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1846">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1847">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1847">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1848">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1848">'Identity'</span></span>
- <span data-ttu-id="6d68c-1849">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1849">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1850">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1850">'Razor'</span></span>
- <span data-ttu-id="6d68c-1851">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1851">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1852">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1852">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1853">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1853">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1854">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1854">'Identity'</span></span>
- <span data-ttu-id="6d68c-1855">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1855">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1856">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1856">'Razor'</span></span>
- <span data-ttu-id="6d68c-1857">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1857">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1858">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1858">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1859">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1859">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1860">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1860">'Identity'</span></span>
- <span data-ttu-id="6d68c-1861">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1861">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1862">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1862">'Razor'</span></span>
- <span data-ttu-id="6d68c-1863">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1863">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1864">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1864">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1865">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1865">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1866">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1866">'Identity'</span></span>
- <span data-ttu-id="6d68c-1867">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1867">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1868">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1868">'Razor'</span></span>
- <span data-ttu-id="6d68c-1869">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1869">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1870">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1870">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1871">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1871">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1872">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1872">'Identity'</span></span>
- <span data-ttu-id="6d68c-1873">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1873">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1874">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1874">'Razor'</span></span>
- <span data-ttu-id="6d68c-1875">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1875">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1876">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1876">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1877">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1877">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1878">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1878">'Identity'</span></span>
- <span data-ttu-id="6d68c-1879">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1879">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1880">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1880">'Razor'</span></span>
- <span data-ttu-id="6d68c-1881">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1881">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1882">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1882">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1883">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1883">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1884">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1884">'Identity'</span></span>
- <span data-ttu-id="6d68c-1885">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1885">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1886">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1886">'Razor'</span></span>
- <span data-ttu-id="6d68c-1887">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1887">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1888">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1888">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1889">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1889">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1890">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1890">'Identity'</span></span>
- <span data-ttu-id="6d68c-1891">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1891">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1892">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1892">'Razor'</span></span>
- <span data-ttu-id="6d68c-1893">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1893">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1894">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1894">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1895">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1895">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1896">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1896">'Identity'</span></span>
- <span data-ttu-id="6d68c-1897">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1897">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1898">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1898">'Razor'</span></span>
- <span data-ttu-id="6d68c-1899">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1899">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1900">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1900">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1901">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1901">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1902">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1902">'Identity'</span></span>
- <span data-ttu-id="6d68c-1903">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1903">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1904">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1904">'Razor'</span></span>
- <span data-ttu-id="6d68c-1905">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1905">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1906">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1906">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1907">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1907">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1908">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1908">'Identity'</span></span>
- <span data-ttu-id="6d68c-1909">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1909">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1910">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1910">'Razor'</span></span>
- <span data-ttu-id="6d68c-1911">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1911">'SignalR' uid:</span></span> 

-
<span data-ttu-id="6d68c-1912">title: Yazar: Açıklama: monikerRange: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1912">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="6d68c-1913">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1913">'Blazor'</span></span>
- <span data-ttu-id="6d68c-1914">'Identity'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1914">'Identity'</span></span>
- <span data-ttu-id="6d68c-1915">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1915">'Let's Encrypt'</span></span>
- <span data-ttu-id="6d68c-1916">'Razor'</span><span class="sxs-lookup"><span data-stu-id="6d68c-1916">'Razor'</span></span>
- <span data-ttu-id="6d68c-1917">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="6d68c-1917">'SignalR' uid:</span></span> 

<span data-ttu-id="6d68c-1918">------------------- | | Yolu QueryString 'e yeniden yazın |`^path/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1918">------------------- | | Rewrite path into querystring | `^path/(.*)/(.*)`</span></span><br>`/path/abc/123` | `path?var1=$1&var2=$2`<br><span data-ttu-id="6d68c-1919">`/path?var1=abc&var2=123`| | Bölme sonunda eğik çizgi |`(.*)/$`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1919">`/path?var1=abc&var2=123` | | Strip trailing slash | `(.*)/$`</span></span><br>`/path/` | `$1`<br><span data-ttu-id="6d68c-1920">`/path`| | Sondaki eğik çizgiyi zorla |`(.*[^/])$`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1920">`/path` | | Enforce trailing slash | `(.*[^/])$`</span></span><br>`/path` | `$1/`<br><span data-ttu-id="6d68c-1921">`/path/`| | Belirli istekleri yeniden yazmayı önleyin | `^(.*)(?<!\.axd)$`ya da`^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1921">`/path/` | | Avoid rewriting specific requests | `^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="6d68c-1922">Yes`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1922">Yes: `/resource.htm`</span></span><br><span data-ttu-id="6d68c-1923">Eşleşen`/resource.axd` | `rewritten/$1`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1923">No: `/resource.axd` | `rewritten/$1`</span></span><br>`/rewritten/resource.htm`<br><span data-ttu-id="6d68c-1924">`/resource.axd`| | URL segmentlerini yeniden Düzenle |`path/(.*)/(.*)/(.*)`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1924">`/resource.axd` | | Rearrange URL segments | `path/(.*)/(.*)/(.*)`</span></span><br>`path/1/2/3` | `path/$3/$2/$1`<br><span data-ttu-id="6d68c-1925">`path/3/2/1`| | URL segmentini değiştirme |`^(.*)/segment2/(.*)`</span><span class="sxs-lookup"><span data-stu-id="6d68c-1925">`path/3/2/1` | | Replace a URL segment | `^(.*)/segment2/(.*)`</span></span><br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="6d68c-1926">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="6d68c-1926">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="6d68c-1927">.NET içinde normal ifadeler</span><span class="sxs-lookup"><span data-stu-id="6d68c-1927">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="6d68c-1928">Normal ifade dili-hızlı başvuru</span><span class="sxs-lookup"><span data-stu-id="6d68c-1928">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="6d68c-1929">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="6d68c-1929">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="6d68c-1930">URL yeniden yazma modülünü kullanma 2,0 (IIS için)</span><span class="sxs-lookup"><span data-stu-id="6d68c-1930">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="6d68c-1931">URL yeniden yazma modülü yapılandırma başvurusu</span><span class="sxs-lookup"><span data-stu-id="6d68c-1931">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="6d68c-1932">IIS URL yeniden yazma modülü Forumu</span><span class="sxs-lookup"><span data-stu-id="6d68c-1932">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="6d68c-1933">Basit URL yapısını saklama</span><span class="sxs-lookup"><span data-stu-id="6d68c-1933">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="6d68c-1934">10 URL yeniden yazma Ipuçları ve püf noktaları</span><span class="sxs-lookup"><span data-stu-id="6d68c-1934">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="6d68c-1935">Eğik çizgi veya eğik çizgi</span><span class="sxs-lookup"><span data-stu-id="6d68c-1935">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
