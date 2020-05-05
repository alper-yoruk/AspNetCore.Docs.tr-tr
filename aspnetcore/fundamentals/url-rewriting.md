---
title: ASP.NET Core içinde URL yeniden yazma ara yazılımı
author: rick-anderson
description: ASP.NET Core uygulamalarında URL yeniden yazma ve URL yeniden yazma ara yazılımı ile yeniden yönlendirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/url-rewriting
ms.openlocfilehash: 9e12831f57af02cd427d2a66d9d4c4d654905106
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774866"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="8b067-103">ASP.NET Core içinde URL yeniden yazma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="8b067-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="8b067-104">X [MIKAEL Mengistu](https://github.com/mikaelm12) tarafından</span><span class="sxs-lookup"><span data-stu-id="8b067-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8b067-105">Bu belgede, ASP.NET Core uygulamalarında URL yeniden yazma ara yazılımı kullanma yönergeleriyle birlikte URL yeniden yazma tanıtılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8b067-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="8b067-106">URL yeniden yazma, istek URL 'Lerini bir veya daha fazla önceden tanımlanmış kurala göre değiştirme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="8b067-107">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmaması için kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8b067-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="8b067-108">URL yeniden yazma işlemi birkaç senaryoda yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="8b067-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="8b067-109">Sunucu kaynaklarını geçici olarak veya kalıcı olarak taşıyın veya değiştirin ve bu kaynakların kararlı konum belirleyicilerinin bakımını yapın.</span><span class="sxs-lookup"><span data-stu-id="8b067-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="8b067-110">İstek işlemeyi farklı uygulamalar arasında veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="8b067-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="8b067-111">Gelen isteklerde URL segmentlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="8b067-112">Arama motoru Iyileştirmesi (SEO) için genel URL 'Leri iyileştirin.</span><span class="sxs-lookup"><span data-stu-id="8b067-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="8b067-113">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmeye yardımcı olmak için kolay genel URL 'Lerin kullanılmasına izin verme.</span><span class="sxs-lookup"><span data-stu-id="8b067-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="8b067-114">Güvensiz istekleri güvenli uç noktalara yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="8b067-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="8b067-115">Bir dış sitenin varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı Hotlink 'i engelleyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="8b067-116">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="8b067-117">Uygun yerlerde kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="8b067-118">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b067-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="8b067-119">URL yeniden yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="8b067-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="8b067-120">*URL yeniden yönlendirme* ve *URL yeniden yazma* arasındaki ifade farkı daha hafif ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="8b067-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="8b067-121">ASP.NET Core URL yeniden yazma ara yazılımı her ikisine de ihtiyacı verebilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="8b067-122">*URL yeniden yönlendirme* , istemcinin, ilk olarak istenen istemciden farklı bir adresteki kaynağa erişmesi için bir istemci tarafı işlemi içerir.</span><span class="sxs-lookup"><span data-stu-id="8b067-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="8b067-123">Bu, sunucuya gidiş dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-123">This requires a round trip to the server.</span></span> <span data-ttu-id="8b067-124">İstemci kaynak için yeni bir istek yaptığında, istemciye döndürülen yeniden yönlendirme URL 'SI tarayıcının adres çubuğunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="8b067-125">Öğesine `/resource` `/different-resource` *yönlendiriliyorsa* sunucu, istemcinin, yeniden yönlendirmenin geçici ya da kalıcı olduğunu belirten bir `/different-resource` durum kodu ile kaynağı elde etmesi gerektiğini yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="8b067-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmeti uç noktası, sürüm 1 ' den (v1) sunucudaki sürüm 2 ' ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="8b067-131">İstekleri farklı bir URL 'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yeniden yönlendirmenin kalıcı mi yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="8b067-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="8b067-132">*301-taşınan kalıcı* durum kodu, kaynağın yeni, kalıcı bir URL olduğu ve istemciye, gelecekteki tüm isteklerin kaynak için tüm istekleri yeni URL 'yi kullanması gerektiğini bildirmek istediğinizde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b067-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="8b067-133">*İstemci, 301 durum kodu alındığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="8b067-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="8b067-134">*302-bulunan* durum kodu, yeniden yönlendirmenin geçici ya da genellikle değişikliğe tabi olduğu durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b067-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="8b067-135">302 durum kodu, istemcinin URL 'YI depolayamadığını ve gelecekte kullanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="8b067-136">Durum kodları hakkında daha fazla bilgi için bkz. [RFC 2616: durum kodu tanımları](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="8b067-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="8b067-137">*URL yeniden yazma* , istenen istemciden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="8b067-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="8b067-138">URL yeniden yazma, sunucuya gidiş dönüş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="8b067-139">Yeniden yazan URL istemciye döndürülmüyor ve tarayıcının adres çubuğunda görünmüyor.</span><span class="sxs-lookup"><span data-stu-id="8b067-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="8b067-140">`/resource` Öğesine `/different-resource`geri *rewritten* dönerse, sunucu, kaynağını *dahili olarak* getirir ve döndürür `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="8b067-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="8b067-141">İstemci, yeniden yazan URL 'de kaynağı alabiliyor olsa da, istemci isteği yaptığında ve yanıtı aldığında kaynağın yeniden yazan URL 'de bulunduğunu bilgilendirmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Sunucu üzerinde sürüm 1 ' den (v1) sürüm 2 ' ye (v2) bir WebAPI hizmet uç noktası değiştirilmiştir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="8b067-146">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="8b067-146">URL rewriting sample app</span></span>

<span data-ttu-id="8b067-147">[Örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)birlikte YENIDEN yazma URL 'sinin özelliklerini inceleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b067-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="8b067-148">Uygulama yeniden yönlendirme ve yeniden yazma kuralları uygular ve birkaç senaryo için yeniden yönlendirilen veya yeniden yazan URL 'YI gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="8b067-149">URL yeniden yazma ara yazılımı ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="8b067-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="8b067-150">Aşağıdaki yaklaşımlardan birini kullandığımmdan URL yeniden yazma ara yazılımı kullanın:</span><span class="sxs-lookup"><span data-stu-id="8b067-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="8b067-151">Windows Server 'da IIS ile URL yeniden yazma modülü</span><span class="sxs-lookup"><span data-stu-id="8b067-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="8b067-152">Apache Server 'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="8b067-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="8b067-153">NGINX üzerinde URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="8b067-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="8b067-154">Ayrıca, uygulama [http. sys sunucusunda](xref:fundamentals/servers/httpsys) barındırıldığı zaman ara yazılımı kullanın (eski adıyla webListener olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="8b067-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="8b067-155">IIS, Apache ve NGINX 'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8b067-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="8b067-156">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="8b067-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="8b067-157">Sunucu modüllerinin bazı özellikleri, IIS yeniden yazma modülünün `IsFile` ve `IsDirectory` kısıtlamaları gibi ASP.NET Core projelerle birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8b067-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="8b067-158">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="8b067-159">Ara yazılım performansı büyük olasılıkla modüllerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="8b067-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="8b067-160">Sınama, performansı en iyi şekilde düşürür veya performans düşüklüğü göz ardı edilebilir olduğundan emin olmanın tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="8b067-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="8b067-161">Paket</span><span class="sxs-lookup"><span data-stu-id="8b067-161">Package</span></span>

<span data-ttu-id="8b067-162">URL yeniden yazma ara yazılımı, ASP.NET Core uygulamalarında örtük olarak bulunan [Microsoft. AspNetCore. yeniden yazma](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="8b067-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="8b067-163">Uzantı ve Seçenekler</span><span class="sxs-lookup"><span data-stu-id="8b067-163">Extension and options</span></span>

<span data-ttu-id="8b067-164">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriyle [Rewriteoptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir ÖRNEĞINI oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="8b067-165">Birden çok kuralı, işlenmeyi istediğiniz sırada zincirle.</span><span class="sxs-lookup"><span data-stu-id="8b067-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="8b067-166">, `RewriteOptions` İle istek ardışık düzenine eklendikçe, bu URL 'ye yeniden yazma ara yazılımı ile <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>geçirilir:</span><span class="sxs-lookup"><span data-stu-id="8b067-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="8b067-167">Www olmayan www 'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8b067-167">Redirect non-www to www</span></span>

<span data-ttu-id="8b067-168">Üç seçenek, uygulamanın`www` istek olmayan istekleri yeniden yönlendirmesine izin verir `www`:</span><span class="sxs-lookup"><span data-stu-id="8b067-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="8b067-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; İstek değilse isteği alt `www` etki alanına kalıcı olarak yeniden yönlendirin.`www`</span><span class="sxs-lookup"><span data-stu-id="8b067-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="8b067-170">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="8b067-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Gelen istek değilse isteği alt `www` etki alanına yönlendirin`www`.</span><span class="sxs-lookup"><span data-stu-id="8b067-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="8b067-172">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="8b067-173">Aşırı yükleme, yanıt için durum kodu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="8b067-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="8b067-174">Bir durum kodu ataması için <xref:Microsoft.AspNetCore.Http.StatusCodes> sınıfın bir alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="8b067-175">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8b067-175">URL redirect</span></span>

<span data-ttu-id="8b067-176">İstekleri <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="8b067-177">İlk parametre gelen URL 'nin yolu ile eşleşen Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="8b067-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="8b067-178">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="8b067-179">Varsa, üçüncü parametre durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="8b067-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="8b067-180">Durum kodunu belirtmezseniz, durum kodu varsayılan olarak *302-bulunur*; bu da kaynağın geçici olarak taşındığını veya değiştirildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="8b067-181">Geliştirici araçları etkinleştirilmiş bir tarayıcıda, örnek uygulamaya yol `/redirect-rule/1234/5678`ile bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="8b067-182">Regex, üzerindeki `redirect-rule/(.*)`istek yoluyla eşleşir ve yol ile `/redirected/1234/5678`değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8b067-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="8b067-183">Yeniden yönlendirme URL 'SI, *302 tarafından bulunan* bir durum kodu ile istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="8b067-184">Tarayıcı, tarayıcının adres çubuğunda görüntülenen yeniden yönlendirme URL 'SI üzerinde yeni bir istek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8b067-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="8b067-185">Örnek uygulamadaki hiçbir kural, yeniden yönlendirme URL 'SI üzerinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="8b067-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="8b067-186">İkinci istek uygulamadan *200-Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="8b067-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="8b067-187">Yanıtın gövdesi, yeniden yönlendirme URL 'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="8b067-188">Bir URL *yeniden yönlendirildiğinde*sunucuya gidiş dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="8b067-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="8b067-189">Yeniden yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="8b067-190">Yeniden yönlendirme kuralları, bir yeniden yönlendirmeden sonra dahil olmak üzere, uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="8b067-191">Yanlışlıkla *sonsuz yeniden yönlendirmeler döngüsü*oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="8b067-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="8b067-192">Özgün Istek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8b067-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="8b067-194">Parantez içinde yer alan ifadenin kısmına bir *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="8b067-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="8b067-195">İfadenin nokta (`.`), *herhangi bir karakterle eşleşir*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="8b067-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="8b067-196">Yıldız işareti (`*`) *, önceki karakterle sıfır veya daha fazla kez eşleşme*gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="8b067-197">Bu nedenle, URL `1234/5678`'nin son iki yol kesimi, yakalama grubu `(.*)`tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="8b067-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="8b067-198">Sonrasında istek URL 'sinde sağladığınız herhangi bir değer bu `redirect-rule/` tek yakalama grubu tarafından yakalandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="8b067-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="8b067-199">Değiştirme dizesinde, yakalanan gruplar, dolar işareti (`$`) ve ardından yakalamanın sıra numarası ile birlikte dizeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="8b067-200">İlk yakalama grubu değeri ile `$1`elde edilir, ikincisi ile `$2`ve normal Regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="8b067-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="8b067-201">Örnek uygulamadaki yeniden yönlendirme kuralı Regex bölümünde yalnızca bir tane yakalanan grup bulunur, bu nedenle değiştirme dizesinde yalnızca bir tane eklenmiş grup vardır `$1`.</span><span class="sxs-lookup"><span data-stu-id="8b067-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="8b067-202">Kural uygulandığında, URL olur `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8b067-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="8b067-203">Güvenli bir uç noktaya URL yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="8b067-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="8b067-204">HTTP <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> isteklerini https protokolünü kullanarak aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="8b067-205">Durum kodu sağlanmazsa, ara yazılım varsayılan olarak *302-bulunur*.</span><span class="sxs-lookup"><span data-stu-id="8b067-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="8b067-206">Bağlantı noktası sağlanmazsa:</span><span class="sxs-lookup"><span data-stu-id="8b067-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="8b067-207">Ara yazılım varsayılan olarak `null`olur.</span><span class="sxs-lookup"><span data-stu-id="8b067-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="8b067-208">Şema (https Protokolü `https` ) olarak değişir ve istemci, 443 numaralı bağlantı noktasında kaynağa erişir.</span><span class="sxs-lookup"><span data-stu-id="8b067-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="8b067-209">Aşağıdaki örnek, durum kodunun *301-kalıcı olarak taşınacağını* ve bağlantı noktasını 5001 olarak nasıl değiştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="8b067-210">Güvenli <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> olmayan istekleri, bağlantı noktası 443 ÜZERINDE güvenli https Protokolü ile aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="8b067-211">Ara yazılım durum kodunu 301 olarak ayarlar ve *kalıcı olarak taşınır*.</span><span class="sxs-lookup"><span data-stu-id="8b067-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="8b067-212">Ek yeniden yönlendirme kuralları gereksinimi olmadan güvenli bir uç noktaya yönlendirilirken, HTTPS yeniden yönlendirme ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="8b067-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="8b067-213">Daha fazla bilgi için bkz. [https 'Yi zorla](xref:security/enforcing-ssl#require-https) konusu.</span><span class="sxs-lookup"><span data-stu-id="8b067-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="8b067-214">Örnek uygulama, veya `AddRedirectToHttps` `AddRedirectToHttpsPermanent`kullanımını gösterme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="8b067-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="8b067-215">Uzantı yöntemini öğesine ekleyin `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="8b067-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="8b067-216">Herhangi bir URL 'de uygulamaya güvenli olmayan bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="8b067-217">Otomatik olarak imzalanan sertifikanın güvenilmeyen tarayıcı güvenlik uyarısını kapatın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="8b067-218">Kullanarak `AddRedirectToHttps(301, 5001)`özgün istek:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8b067-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="8b067-220">Kullanarak `AddRedirectToHttpsPermanent`özgün istek:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8b067-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="8b067-222">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="8b067-222">URL rewrite</span></span>

<span data-ttu-id="8b067-223">URL <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 'leri yeniden yazma kuralı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="8b067-224">İlk parametre gelen URL yolundaki eşleşme için Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="8b067-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="8b067-225">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="8b067-226">Üçüncü parametresi `skipRemainingRules: {true|false}`, geçerli kural uygulanmışsa ek yeniden yazma kurallarının atlanıp atlanmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="8b067-227">Özgün Istek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8b067-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="8b067-229">İfadenin başındaki simgeyi seçtiğinizde`^`(), eşleşmesinin URL yolunun başlangıcında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="8b067-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="8b067-230">Önceki örnekte, yeniden yönlendirme kuralıyla `redirect-rule/(.*)`, Regex başlangıcında simgeyi seçtiğinizde (`^`) yoktur.</span><span class="sxs-lookup"><span data-stu-id="8b067-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="8b067-231">Bu nedenle, başarılı bir eşleşme `redirect-rule/` için herhangi bir karakterden önce yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="8b067-232">Yol</span><span class="sxs-lookup"><span data-stu-id="8b067-232">Path</span></span>                               | <span data-ttu-id="8b067-233">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="8b067-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="8b067-234">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="8b067-235">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="8b067-236">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-236">Yes</span></span>   |

<span data-ttu-id="8b067-237">Yeniden yazma kuralı `^rewrite-rule/(\d+)/(\d+)`, yalnızca ile `rewrite-rule/`başlarsa yollarla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="8b067-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="8b067-238">Aşağıdaki tabloda, eşleşen farkı aklınızda.</span><span class="sxs-lookup"><span data-stu-id="8b067-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="8b067-239">Yol</span><span class="sxs-lookup"><span data-stu-id="8b067-239">Path</span></span>                              | <span data-ttu-id="8b067-240">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="8b067-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="8b067-241">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="8b067-242">Hayır</span><span class="sxs-lookup"><span data-stu-id="8b067-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="8b067-243">Hayır</span><span class="sxs-lookup"><span data-stu-id="8b067-243">No</span></span>    |

<span data-ttu-id="8b067-244">İfadenin `^rewrite-rule/` bölümünü takip eden iki yakalama grubu vardır `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="8b067-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="8b067-245">`\d` Belirtir *bir sayıyla (sayı) eşleşir*.</span><span class="sxs-lookup"><span data-stu-id="8b067-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="8b067-246">Artı işareti (`+`) *bir veya daha fazla önceki karakterden eşleşiyor*demektir.</span><span class="sxs-lookup"><span data-stu-id="8b067-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="8b067-247">Bu nedenle, URL bir sayı içermeli ve ardından İleri eğik çizgi ve ardından başka bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="8b067-248">Bu yakalama grupları, ve olarak `$1` YENIDEN `$2`yazan URL 'sine eklenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="8b067-249">Yeniden yazma kuralı değiştirme dizesi yakalanan grupları sorgu dizesine koyar.</span><span class="sxs-lookup"><span data-stu-id="8b067-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="8b067-250">İstenen yolu, kaynağı `/rewrite-rule/1234/5678` elde etmek için yeniden yazılır `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="8b067-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="8b067-251">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazdığınızda korunur.</span><span class="sxs-lookup"><span data-stu-id="8b067-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="8b067-252">Kaynağı almak için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="8b067-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="8b067-253">Kaynak varsa, bu, alınır ve istemciye *200-ok* durum kodu ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="8b067-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="8b067-254">İstemci yeniden yönlendirmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="8b067-255">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamaz.</span><span class="sxs-lookup"><span data-stu-id="8b067-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="8b067-256">Eşleşen `skipRemainingRules: true` kuralların hesaplama maliyeti ve uygulama yanıt süresini arttığı için mümkün olan her durumda kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="8b067-257">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="8b067-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="8b067-258">En sık eşleşen kuraldan en az sıklıkta eşleşen kurala göre yeniden yazma kuralları.</span><span class="sxs-lookup"><span data-stu-id="8b067-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="8b067-259">Bir eşleşme gerçekleştiğinde ve ek kural işleme gerekli olmadığında kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="8b067-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="8b067-260">Apache mod_rewrite</span></span>

<span data-ttu-id="8b067-261">İle <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>Apache mod_rewrite kuralları uygulayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="8b067-262">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8b067-263">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için bkz. [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="8b067-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="8b067-264"><xref:System.IO.StreamReader> , *ApacheModRewrite. txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="8b067-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="8b067-265">Örnek uygulama, istekleri ' den `/apache-mod-rules-redirect/(.\*)` ' `/redirected?id=$1`e yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="8b067-266">Yanıt durum kodu *302-bulundu*.</span><span class="sxs-lookup"><span data-stu-id="8b067-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="8b067-267">Özgün Istek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="8b067-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="8b067-269">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="8b067-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="8b067-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="8b067-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8b067-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8b067-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8b067-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8b067-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8b067-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="8b067-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="8b067-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="8b067-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8b067-275">HTTP_HOST</span></span>
* <span data-ttu-id="8b067-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8b067-276">HTTP_REFERER</span></span>
* <span data-ttu-id="8b067-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8b067-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8b067-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b067-278">HTTPS</span></span>
* <span data-ttu-id="8b067-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="8b067-279">IPV6</span></span>
* <span data-ttu-id="8b067-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8b067-280">QUERY_STRING</span></span>
* <span data-ttu-id="8b067-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="8b067-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8b067-282">REMOTE_PORT</span></span>
* <span data-ttu-id="8b067-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8b067-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8b067-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="8b067-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="8b067-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="8b067-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="8b067-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8b067-286">REQUEST_URI</span></span>
* <span data-ttu-id="8b067-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8b067-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="8b067-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-288">SERVER_ADDR</span></span>
* <span data-ttu-id="8b067-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="8b067-289">SERVER_PORT</span></span>
* <span data-ttu-id="8b067-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="8b067-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="8b067-291">TIME</span><span class="sxs-lookup"><span data-stu-id="8b067-291">TIME</span></span>
* <span data-ttu-id="8b067-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="8b067-292">TIME_DAY</span></span>
* <span data-ttu-id="8b067-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="8b067-293">TIME_HOUR</span></span>
* <span data-ttu-id="8b067-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="8b067-294">TIME_MIN</span></span>
* <span data-ttu-id="8b067-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="8b067-295">TIME_MON</span></span>
* <span data-ttu-id="8b067-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="8b067-296">TIME_SEC</span></span>
* <span data-ttu-id="8b067-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="8b067-297">TIME_WDAY</span></span>
* <span data-ttu-id="8b067-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="8b067-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="8b067-299">IIS URL yeniden yazma modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="8b067-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="8b067-300">IIS URL yeniden yazma modülü için geçerli olan kural kümesini kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="8b067-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="8b067-301">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8b067-302">Windows Server IIS 'de çalışırken uygulamanın *Web. config* dosyasını kullanmak için ara yazılımı yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="8b067-303">IIS ile, IIS yeniden yazma modülüyle çakışmalardan kaçınmak için bu kuralların uygulamanın *Web. config* dosyası dışında depolanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8b067-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="8b067-304">Daha fazla bilgi ve IIS URL yeniden yazma modülü kurallarının örnekleri için bkz. [Using URL yeniden yazma modülü 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) ve [URL yeniden yazma modülü yapılandırma başvurusu](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="8b067-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="8b067-305"><xref:System.IO.StreamReader> , *Iisurlyeniden yazma. xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="8b067-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="8b067-306">Örnek uygulama, ' den ' `/iis-rules-rewrite/(.*)` e `/rewritten?id=$1`olan istekleri yeniden yazar.</span><span class="sxs-lookup"><span data-stu-id="8b067-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="8b067-307">Yanıt, istemciye *200-ok* durum kodu ile gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="8b067-308">Özgün Istek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="8b067-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="8b067-310">Uygulamanızı istenmeyen yollarla etkileyebilecek sunucu düzeyi kurallara sahip etkin bir IIS yeniden yazma modülünüzün olması halinde, bir uygulama için IIS yeniden yazma modülünü devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b067-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="8b067-311">Daha fazla bilgi için bkz. [IIS modüllerini devre dışı bırakma](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="8b067-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="8b067-312">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="8b067-312">Unsupported features</span></span>

<span data-ttu-id="8b067-313">ASP.NET Core 2. x ile yayınlanan ara yazılım, aşağıdaki IIS URL yeniden yazma modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="8b067-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="8b067-314">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="8b067-314">Outbound Rules</span></span>
* <span data-ttu-id="8b067-315">Özel sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="8b067-315">Custom Server Variables</span></span>
* <span data-ttu-id="8b067-316">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="8b067-316">Wildcards</span></span>
* <span data-ttu-id="8b067-317">LogRewrittenUrl 'Si</span><span class="sxs-lookup"><span data-stu-id="8b067-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="8b067-318">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="8b067-318">Supported server variables</span></span>

<span data-ttu-id="8b067-319">Ara yazılım aşağıdaki IIS URL yeniden yazma modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="8b067-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="8b067-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="8b067-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="8b067-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="8b067-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="8b067-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8b067-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8b067-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8b067-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8b067-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8b067-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="8b067-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8b067-325">HTTP_HOST</span></span>
* <span data-ttu-id="8b067-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8b067-326">HTTP_REFERER</span></span>
* <span data-ttu-id="8b067-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="8b067-327">HTTP_URL</span></span>
* <span data-ttu-id="8b067-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8b067-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8b067-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b067-329">HTTPS</span></span>
* <span data-ttu-id="8b067-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="8b067-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8b067-331">QUERY_STRING</span></span>
* <span data-ttu-id="8b067-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="8b067-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8b067-333">REMOTE_PORT</span></span>
* <span data-ttu-id="8b067-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8b067-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8b067-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8b067-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="8b067-336">Ayrıca bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> ile elde edebilirsiniz <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="8b067-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="8b067-337">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="8b067-338">Yeniden yazma kuralları dosyalarınızın sağladığınız yoldaki sunucuya dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="8b067-339">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="8b067-339">Method-based rule</span></span>

<span data-ttu-id="8b067-340">Bir <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="8b067-341">`Add`, metodunda <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>kullanım <xref:Microsoft.AspNetCore.Http.HttpContext> için kullanılabilir hale getiren öğesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="8b067-342">[Rewritecontext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) , ek ardışık düzen işlemenin nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="8b067-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="8b067-343">Değeri aşağıdaki tabloda açıklanan <xref:Microsoft.AspNetCore.Rewrite.RuleResult> alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="8b067-344">Eylem</span><span class="sxs-lookup"><span data-stu-id="8b067-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="8b067-345">`RuleResult.ContinueRules`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="8b067-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="8b067-346">Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="8b067-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="8b067-347">Kuralları uygulamayı durdurun ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="8b067-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="8b067-348">Kuralları uygulamayı durdurun ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="8b067-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="8b067-349">Örnek uygulama, *. xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="8b067-350">İçin `/file.xml`bir istek yapılırsa, istek öğesine `/xmlfiles/file.xml`yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="8b067-351">Durum kodu *301*olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8b067-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="8b067-352">Tarayıcı */xmlfiles/File.xml*için yeni bir istek yaptığında, statik dosya ara yazılımı dosyayı *Wwwroot/xmlfiles* klasöründen istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="8b067-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="8b067-353">Yeniden yönlendirme için, yanıtın durum kodunu açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="8b067-354">Aksi takdirde, *200-ok* durum kodu döndürülür ve yeniden yönlendirme istemcide gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="8b067-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b067-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="8b067-356">Bu yaklaşım ayrıca istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="8b067-357">Örnek uygulama, *dosya. txt* metin dosyasına *Wwwroot* klasöründen hizmeti sağlamak için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="8b067-358">Statik dosya ara yazılımı, güncelleştirilmiş istek yoluna göre dosyayı sunar:</span><span class="sxs-lookup"><span data-stu-id="8b067-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="8b067-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b067-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="8b067-360">Irule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="8b067-360">IRule-based rule</span></span>

<span data-ttu-id="8b067-361">Arabirimini uygulayan bir sınıfta kural mantığını kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> <xref:Microsoft.AspNetCore.Rewrite.IRule></span><span class="sxs-lookup"><span data-stu-id="8b067-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="8b067-362">`IRule`Yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="8b067-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="8b067-363">Uygulama sınıfınız, <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> yöntemi için parametreleri geçirebilmeniz için bir Oluşturucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="8b067-364">`extension` Ve `newPath` için örnek uygulamadaki parametrelerin değerleri, çeşitli koşullara uyacak şekilde denetlenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="8b067-365">`extension` Bir değer içermeli ve değer *. png*, *. jpg*veya *. gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8b067-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="8b067-366">Geçerli `newPath` değilse, bir <xref:System.ArgumentException> oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8b067-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="8b067-367">*Image. png*için bir istek yapılırsa, istek öğesine `/png-images/image.png`yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="8b067-368">*Image. jpg*için bir istek yapılırsa, istek öğesine `/jpg-images/image.jpg`yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="8b067-369">Durum kodu 301 olarak ayarlanır ve *kalıcı olarak taşınır*ve `context.Result` kuralları işlemeyi durdur ve yanıtı gönder olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8b067-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="8b067-370">Özgün Istek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="8b067-370">Original Request: `/image.png`</span></span>

![Görüntü. png için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="8b067-372">Özgün Istek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="8b067-372">Original Request: `/image.jpg`</span></span>

![Image. jpg için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="8b067-374">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="8b067-374">Regex examples</span></span>

| <span data-ttu-id="8b067-375">Hedef</span><span class="sxs-lookup"><span data-stu-id="8b067-375">Goal</span></span> | <span data-ttu-id="8b067-376">Regex dize &</span><span class="sxs-lookup"><span data-stu-id="8b067-376">Regex String &</span></span><br><span data-ttu-id="8b067-377">Match örneği</span><span class="sxs-lookup"><span data-stu-id="8b067-377">Match Example</span></span> | <span data-ttu-id="8b067-378">Değiştirme dizesi &</span><span class="sxs-lookup"><span data-stu-id="8b067-378">Replacement String &</span></span><br><span data-ttu-id="8b067-379">Çıkış örneği</span><span class="sxs-lookup"><span data-stu-id="8b067-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="8b067-380">Yolu QueryString 'e yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="8b067-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="8b067-381">Eğik çizgiyi çıkar</span><span class="sxs-lookup"><span data-stu-id="8b067-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="8b067-382">Sondaki eğik çizgiyi zorla</span><span class="sxs-lookup"><span data-stu-id="8b067-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="8b067-383">Belirli istekleri yeniden yazmayı önleyin</span><span class="sxs-lookup"><span data-stu-id="8b067-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="8b067-384">`^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="8b067-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="8b067-385">Yes`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="8b067-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="8b067-386">Eşleşen`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="8b067-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="8b067-387">URL segmentlerini yeniden Düzenle</span><span class="sxs-lookup"><span data-stu-id="8b067-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="8b067-388">URL segmentini değiştirme</span><span class="sxs-lookup"><span data-stu-id="8b067-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8b067-389">Bu belgede, ASP.NET Core uygulamalarında URL yeniden yazma ara yazılımı kullanma yönergeleriyle birlikte URL yeniden yazma tanıtılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="8b067-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="8b067-390">URL yeniden yazma, istek URL 'Lerini bir veya daha fazla önceden tanımlanmış kurala göre değiştirme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="8b067-391">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmaması için kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8b067-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="8b067-392">URL yeniden yazma işlemi birkaç senaryoda yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="8b067-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="8b067-393">Sunucu kaynaklarını geçici olarak veya kalıcı olarak taşıyın veya değiştirin ve bu kaynakların kararlı konum belirleyicilerinin bakımını yapın.</span><span class="sxs-lookup"><span data-stu-id="8b067-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="8b067-394">İstek işlemeyi farklı uygulamalar arasında veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="8b067-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="8b067-395">Gelen isteklerde URL segmentlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="8b067-396">Arama motoru Iyileştirmesi (SEO) için genel URL 'Leri iyileştirin.</span><span class="sxs-lookup"><span data-stu-id="8b067-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="8b067-397">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmeye yardımcı olmak için kolay genel URL 'Lerin kullanılmasına izin verme.</span><span class="sxs-lookup"><span data-stu-id="8b067-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="8b067-398">Güvensiz istekleri güvenli uç noktalara yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="8b067-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="8b067-399">Bir dış sitenin varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı Hotlink 'i engelleyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="8b067-400">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="8b067-401">Uygun yerlerde kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="8b067-402">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="8b067-402">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="8b067-403">URL yeniden yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="8b067-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="8b067-404">*URL yeniden yönlendirme* ve *URL yeniden yazma* arasındaki ifade farkı daha hafif ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="8b067-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="8b067-405">ASP.NET Core URL yeniden yazma ara yazılımı her ikisine de ihtiyacı verebilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="8b067-406">*URL yeniden yönlendirme* , istemcinin, ilk olarak istenen istemciden farklı bir adresteki kaynağa erişmesi için bir istemci tarafı işlemi içerir.</span><span class="sxs-lookup"><span data-stu-id="8b067-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="8b067-407">Bu, sunucuya gidiş dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-407">This requires a round trip to the server.</span></span> <span data-ttu-id="8b067-408">İstemci kaynak için yeni bir istek yaptığında, istemciye döndürülen yeniden yönlendirme URL 'SI tarayıcının adres çubuğunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="8b067-409">Öğesine `/resource` `/different-resource` *yönlendiriliyorsa* sunucu, istemcinin, yeniden yönlendirmenin geçici ya da kalıcı olduğunu belirten bir `/different-resource` durum kodu ile kaynağı elde etmesi gerektiğini yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="8b067-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmeti uç noktası, sürüm 1 ' den (v1) sunucudaki sürüm 2 ' ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="8b067-415">İstekleri farklı bir URL 'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yeniden yönlendirmenin kalıcı mi yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="8b067-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="8b067-416">*301-taşınan kalıcı* durum kodu, kaynağın yeni, kalıcı bir URL olduğu ve istemciye, gelecekteki tüm isteklerin kaynak için tüm istekleri yeni URL 'yi kullanması gerektiğini bildirmek istediğinizde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b067-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="8b067-417">*İstemci, 301 durum kodu alındığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="8b067-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="8b067-418">*302-bulunan* durum kodu, yeniden yönlendirmenin geçici ya da genellikle değişikliğe tabi olduğu durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="8b067-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="8b067-419">302 durum kodu, istemcinin URL 'YI depolayamadığını ve gelecekte kullanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="8b067-420">Durum kodları hakkında daha fazla bilgi için bkz. [RFC 2616: durum kodu tanımları](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="8b067-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="8b067-421">*URL yeniden yazma* , istenen istemciden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="8b067-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="8b067-422">URL yeniden yazma, sunucuya gidiş dönüş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="8b067-423">Yeniden yazan URL istemciye döndürülmüyor ve tarayıcının adres çubuğunda görünmüyor.</span><span class="sxs-lookup"><span data-stu-id="8b067-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="8b067-424">`/resource` Öğesine `/different-resource`geri *rewritten* dönerse, sunucu, kaynağını *dahili olarak* getirir ve döndürür `/different-resource`.</span><span class="sxs-lookup"><span data-stu-id="8b067-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="8b067-425">İstemci, yeniden yazan URL 'de kaynağı alabiliyor olsa da, istemci isteği yaptığında ve yanıtı aldığında kaynağın yeniden yazan URL 'de bulunduğunu bilgilendirmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Sunucu üzerinde sürüm 1 ' den (v1) sürüm 2 ' ye (v2) bir WebAPI hizmet uç noktası değiştirilmiştir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="8b067-430">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="8b067-430">URL rewriting sample app</span></span>

<span data-ttu-id="8b067-431">[Örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)birlikte YENIDEN yazma URL 'sinin özelliklerini inceleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b067-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="8b067-432">Uygulama yeniden yönlendirme ve yeniden yazma kuralları uygular ve birkaç senaryo için yeniden yönlendirilen veya yeniden yazan URL 'YI gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="8b067-433">URL yeniden yazma ara yazılımı ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="8b067-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="8b067-434">Aşağıdaki yaklaşımlardan birini kullandığımmdan URL yeniden yazma ara yazılımı kullanın:</span><span class="sxs-lookup"><span data-stu-id="8b067-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="8b067-435">Windows Server 'da IIS ile URL yeniden yazma modülü</span><span class="sxs-lookup"><span data-stu-id="8b067-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="8b067-436">Apache Server 'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="8b067-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="8b067-437">NGINX üzerinde URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="8b067-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="8b067-438">Ayrıca, uygulama [http. sys sunucusunda](xref:fundamentals/servers/httpsys) barındırıldığı zaman ara yazılımı kullanın (eski adıyla webListener olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="8b067-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="8b067-439">IIS, Apache ve NGINX 'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="8b067-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="8b067-440">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="8b067-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="8b067-441">Sunucu modüllerinin bazı özellikleri, IIS yeniden yazma modülünün `IsFile` ve `IsDirectory` kısıtlamaları gibi ASP.NET Core projelerle birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="8b067-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="8b067-442">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="8b067-443">Ara yazılım performansı büyük olasılıkla modüllerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="8b067-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="8b067-444">Sınama, performansı en iyi şekilde düşürür veya performans düşüklüğü göz ardı edilebilir olduğundan emin olmanın tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="8b067-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="8b067-445">Paket</span><span class="sxs-lookup"><span data-stu-id="8b067-445">Package</span></span>

<span data-ttu-id="8b067-446">Ara yazılımı projenize dahil etmek için, [Microsoft. aspnetcore. yeniden yazma](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketini içeren proje dosyasındaki [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app) öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="8b067-447">`Microsoft.AspNetCore.App` Metapackage 'i kullanmıyorsanız, `Microsoft.AspNetCore.Rewrite` pakete bir proje başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="8b067-448">Uzantı ve Seçenekler</span><span class="sxs-lookup"><span data-stu-id="8b067-448">Extension and options</span></span>

<span data-ttu-id="8b067-449">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriyle [Rewriteoptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir ÖRNEĞINI oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="8b067-450">Birden çok kuralı, işlenmeyi istediğiniz sırada zincirle.</span><span class="sxs-lookup"><span data-stu-id="8b067-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="8b067-451">, `RewriteOptions` İle istek ardışık düzenine eklendikçe, bu URL 'ye yeniden yazma ara yazılımı ile <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>geçirilir:</span><span class="sxs-lookup"><span data-stu-id="8b067-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="8b067-452">Www olmayan www 'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8b067-452">Redirect non-www to www</span></span>

<span data-ttu-id="8b067-453">Üç seçenek, uygulamanın`www` istek olmayan istekleri yeniden yönlendirmesine izin verir `www`:</span><span class="sxs-lookup"><span data-stu-id="8b067-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="8b067-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; İstek değilse isteği alt `www` etki alanına kalıcı olarak yeniden yönlendirin.`www`</span><span class="sxs-lookup"><span data-stu-id="8b067-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="8b067-455">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="8b067-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Gelen istek değilse isteği alt `www` etki alanına yönlendirin`www`.</span><span class="sxs-lookup"><span data-stu-id="8b067-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="8b067-457">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="8b067-458">Aşırı yükleme, yanıt için durum kodu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="8b067-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="8b067-459">Bir durum kodu ataması için <xref:Microsoft.AspNetCore.Http.StatusCodes> sınıfın bir alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="8b067-460">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="8b067-460">URL redirect</span></span>

<span data-ttu-id="8b067-461">İstekleri <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="8b067-462">İlk parametre gelen URL 'nin yolu ile eşleşen Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="8b067-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="8b067-463">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="8b067-464">Varsa, üçüncü parametre durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="8b067-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="8b067-465">Durum kodunu belirtmezseniz, durum kodu varsayılan olarak *302-bulunur*; bu da kaynağın geçici olarak taşındığını veya değiştirildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="8b067-466">Geliştirici araçları etkinleştirilmiş bir tarayıcıda, örnek uygulamaya yol `/redirect-rule/1234/5678`ile bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="8b067-467">Regex, üzerindeki `redirect-rule/(.*)`istek yoluyla eşleşir ve yol ile `/redirected/1234/5678`değiştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="8b067-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="8b067-468">Yeniden yönlendirme URL 'SI, *302 tarafından bulunan* bir durum kodu ile istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="8b067-469">Tarayıcı, tarayıcının adres çubuğunda görüntülenen yeniden yönlendirme URL 'SI üzerinde yeni bir istek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="8b067-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="8b067-470">Örnek uygulamadaki hiçbir kural, yeniden yönlendirme URL 'SI üzerinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="8b067-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="8b067-471">İkinci istek uygulamadan *200-Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="8b067-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="8b067-472">Yanıtın gövdesi, yeniden yönlendirme URL 'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="8b067-473">Bir URL *yeniden yönlendirildiğinde*sunucuya gidiş dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="8b067-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="8b067-474">Yeniden yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="8b067-475">Yeniden yönlendirme kuralları, bir yeniden yönlendirmeden sonra dahil olmak üzere, uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="8b067-476">Yanlışlıkla *sonsuz yeniden yönlendirmeler döngüsü*oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="8b067-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="8b067-477">Özgün Istek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8b067-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="8b067-479">Parantez içinde yer alan ifadenin kısmına bir *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="8b067-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="8b067-480">İfadenin nokta (`.`), *herhangi bir karakterle eşleşir*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="8b067-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="8b067-481">Yıldız işareti (`*`) *, önceki karakterle sıfır veya daha fazla kez eşleşme*gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="8b067-482">Bu nedenle, URL `1234/5678`'nin son iki yol kesimi, yakalama grubu `(.*)`tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="8b067-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="8b067-483">Sonrasında istek URL 'sinde sağladığınız herhangi bir değer bu `redirect-rule/` tek yakalama grubu tarafından yakalandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="8b067-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="8b067-484">Değiştirme dizesinde, yakalanan gruplar, dolar işareti (`$`) ve ardından yakalamanın sıra numarası ile birlikte dizeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="8b067-485">İlk yakalama grubu değeri ile `$1`elde edilir, ikincisi ile `$2`ve normal Regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="8b067-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="8b067-486">Örnek uygulamadaki yeniden yönlendirme kuralı Regex bölümünde yalnızca bir tane yakalanan grup bulunur, bu nedenle değiştirme dizesinde yalnızca bir tane eklenmiş grup vardır `$1`.</span><span class="sxs-lookup"><span data-stu-id="8b067-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="8b067-487">Kural uygulandığında, URL olur `/redirected/1234/5678`.</span><span class="sxs-lookup"><span data-stu-id="8b067-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="8b067-488">Güvenli bir uç noktaya URL yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="8b067-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="8b067-489">HTTP <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> isteklerini https protokolünü kullanarak aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="8b067-490">Durum kodu sağlanmazsa, ara yazılım varsayılan olarak *302-bulunur*.</span><span class="sxs-lookup"><span data-stu-id="8b067-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="8b067-491">Bağlantı noktası sağlanmazsa:</span><span class="sxs-lookup"><span data-stu-id="8b067-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="8b067-492">Ara yazılım varsayılan olarak `null`olur.</span><span class="sxs-lookup"><span data-stu-id="8b067-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="8b067-493">Şema (https Protokolü `https` ) olarak değişir ve istemci, 443 numaralı bağlantı noktasında kaynağa erişir.</span><span class="sxs-lookup"><span data-stu-id="8b067-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="8b067-494">Aşağıdaki örnek, durum kodunun *301-kalıcı olarak taşınacağını* ve bağlantı noktasını 5001 olarak nasıl değiştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="8b067-495">Güvenli <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> olmayan istekleri, bağlantı noktası 443 ÜZERINDE güvenli https Protokolü ile aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="8b067-496">Ara yazılım durum kodunu 301 olarak ayarlar ve *kalıcı olarak taşınır*.</span><span class="sxs-lookup"><span data-stu-id="8b067-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="8b067-497">Ek yeniden yönlendirme kuralları gereksinimi olmadan güvenli bir uç noktaya yönlendirilirken, HTTPS yeniden yönlendirme ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="8b067-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="8b067-498">Daha fazla bilgi için bkz. [https 'Yi zorla](xref:security/enforcing-ssl#require-https) konusu.</span><span class="sxs-lookup"><span data-stu-id="8b067-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="8b067-499">Örnek uygulama, veya `AddRedirectToHttps` `AddRedirectToHttpsPermanent`kullanımını gösterme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="8b067-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="8b067-500">Uzantı yöntemini öğesine ekleyin `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="8b067-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="8b067-501">Herhangi bir URL 'de uygulamaya güvenli olmayan bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="8b067-502">Otomatik olarak imzalanan sertifikanın güvenilmeyen tarayıcı güvenlik uyarısını kapatın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="8b067-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="8b067-503">Kullanarak `AddRedirectToHttps(301, 5001)`özgün istek:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8b067-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="8b067-505">Kullanarak `AddRedirectToHttpsPermanent`özgün istek:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="8b067-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="8b067-507">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="8b067-507">URL rewrite</span></span>

<span data-ttu-id="8b067-508">URL <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> 'leri yeniden yazma kuralı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="8b067-509">İlk parametre gelen URL yolundaki eşleşme için Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="8b067-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="8b067-510">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="8b067-511">Üçüncü parametresi `skipRemainingRules: {true|false}`, geçerli kural uygulanmışsa ek yeniden yazma kurallarının atlanıp atlanmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="8b067-512">Özgün Istek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="8b067-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="8b067-514">İfadenin başındaki simgeyi seçtiğinizde`^`(), eşleşmesinin URL yolunun başlangıcında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="8b067-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="8b067-515">Önceki örnekte, yeniden yönlendirme kuralıyla `redirect-rule/(.*)`, Regex başlangıcında simgeyi seçtiğinizde (`^`) yoktur.</span><span class="sxs-lookup"><span data-stu-id="8b067-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="8b067-516">Bu nedenle, başarılı bir eşleşme `redirect-rule/` için herhangi bir karakterden önce yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="8b067-517">Yol</span><span class="sxs-lookup"><span data-stu-id="8b067-517">Path</span></span>                               | <span data-ttu-id="8b067-518">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="8b067-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="8b067-519">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="8b067-520">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="8b067-521">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-521">Yes</span></span>   |

<span data-ttu-id="8b067-522">Yeniden yazma kuralı `^rewrite-rule/(\d+)/(\d+)`, yalnızca ile `rewrite-rule/`başlarsa yollarla eşleşir.</span><span class="sxs-lookup"><span data-stu-id="8b067-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="8b067-523">Aşağıdaki tabloda, eşleşen farkı aklınızda.</span><span class="sxs-lookup"><span data-stu-id="8b067-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="8b067-524">Yol</span><span class="sxs-lookup"><span data-stu-id="8b067-524">Path</span></span>                              | <span data-ttu-id="8b067-525">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="8b067-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="8b067-526">Yes</span><span class="sxs-lookup"><span data-stu-id="8b067-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="8b067-527">Hayır</span><span class="sxs-lookup"><span data-stu-id="8b067-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="8b067-528">Hayır</span><span class="sxs-lookup"><span data-stu-id="8b067-528">No</span></span>    |

<span data-ttu-id="8b067-529">İfadenin `^rewrite-rule/` bölümünü takip eden iki yakalama grubu vardır `(\d+)/(\d+)`.</span><span class="sxs-lookup"><span data-stu-id="8b067-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="8b067-530">`\d` Belirtir *bir sayıyla (sayı) eşleşir*.</span><span class="sxs-lookup"><span data-stu-id="8b067-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="8b067-531">Artı işareti (`+`) *bir veya daha fazla önceki karakterden eşleşiyor*demektir.</span><span class="sxs-lookup"><span data-stu-id="8b067-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="8b067-532">Bu nedenle, URL bir sayı içermeli ve ardından İleri eğik çizgi ve ardından başka bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="8b067-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="8b067-533">Bu yakalama grupları, ve olarak `$1` YENIDEN `$2`yazan URL 'sine eklenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="8b067-534">Yeniden yazma kuralı değiştirme dizesi yakalanan grupları sorgu dizesine koyar.</span><span class="sxs-lookup"><span data-stu-id="8b067-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="8b067-535">İstenen yolu, kaynağı `/rewrite-rule/1234/5678` elde etmek için yeniden yazılır `/rewritten?var1=1234&var2=5678`.</span><span class="sxs-lookup"><span data-stu-id="8b067-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="8b067-536">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazdığınızda korunur.</span><span class="sxs-lookup"><span data-stu-id="8b067-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="8b067-537">Kaynağı almak için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="8b067-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="8b067-538">Kaynak varsa, bu, alınır ve istemciye *200-ok* durum kodu ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="8b067-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="8b067-539">İstemci yeniden yönlendirmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="8b067-540">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamaz.</span><span class="sxs-lookup"><span data-stu-id="8b067-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="8b067-541">Eşleşen `skipRemainingRules: true` kuralların hesaplama maliyeti ve uygulama yanıt süresini arttığı için mümkün olan her durumda kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="8b067-542">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="8b067-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="8b067-543">En sık eşleşen kuraldan en az sıklıkta eşleşen kurala göre yeniden yazma kuralları.</span><span class="sxs-lookup"><span data-stu-id="8b067-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="8b067-544">Bir eşleşme gerçekleştiğinde ve ek kural işleme gerekli olmadığında kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="8b067-545">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="8b067-545">Apache mod_rewrite</span></span>

<span data-ttu-id="8b067-546">İle <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>Apache mod_rewrite kuralları uygulayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="8b067-547">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8b067-548">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için bkz. [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="8b067-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="8b067-549"><xref:System.IO.StreamReader> , *ApacheModRewrite. txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="8b067-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="8b067-550">Örnek uygulama, istekleri ' den `/apache-mod-rules-redirect/(.\*)` ' `/redirected?id=$1`e yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="8b067-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="8b067-551">Yanıt durum kodu *302-bulundu*.</span><span class="sxs-lookup"><span data-stu-id="8b067-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="8b067-552">Özgün Istek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="8b067-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="8b067-554">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="8b067-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="8b067-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="8b067-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8b067-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8b067-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8b067-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8b067-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8b067-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="8b067-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="8b067-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="8b067-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8b067-560">HTTP_HOST</span></span>
* <span data-ttu-id="8b067-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8b067-561">HTTP_REFERER</span></span>
* <span data-ttu-id="8b067-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8b067-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8b067-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b067-563">HTTPS</span></span>
* <span data-ttu-id="8b067-564">IPV6</span><span class="sxs-lookup"><span data-stu-id="8b067-564">IPV6</span></span>
* <span data-ttu-id="8b067-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8b067-565">QUERY_STRING</span></span>
* <span data-ttu-id="8b067-566">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="8b067-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8b067-567">REMOTE_PORT</span></span>
* <span data-ttu-id="8b067-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8b067-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8b067-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="8b067-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="8b067-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="8b067-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="8b067-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8b067-571">REQUEST_URI</span></span>
* <span data-ttu-id="8b067-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8b067-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="8b067-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-573">SERVER_ADDR</span></span>
* <span data-ttu-id="8b067-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="8b067-574">SERVER_PORT</span></span>
* <span data-ttu-id="8b067-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="8b067-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="8b067-576">TIME</span><span class="sxs-lookup"><span data-stu-id="8b067-576">TIME</span></span>
* <span data-ttu-id="8b067-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="8b067-577">TIME_DAY</span></span>
* <span data-ttu-id="8b067-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="8b067-578">TIME_HOUR</span></span>
* <span data-ttu-id="8b067-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="8b067-579">TIME_MIN</span></span>
* <span data-ttu-id="8b067-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="8b067-580">TIME_MON</span></span>
* <span data-ttu-id="8b067-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="8b067-581">TIME_SEC</span></span>
* <span data-ttu-id="8b067-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="8b067-582">TIME_WDAY</span></span>
* <span data-ttu-id="8b067-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="8b067-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="8b067-584">IIS URL yeniden yazma modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="8b067-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="8b067-585">IIS URL yeniden yazma modülü için geçerli olan kural kümesini kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="8b067-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="8b067-586">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="8b067-587">Windows Server IIS 'de çalışırken uygulamanın *Web. config* dosyasını kullanmak için ara yazılımı yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="8b067-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="8b067-588">IIS ile, IIS yeniden yazma modülüyle çakışmalardan kaçınmak için bu kuralların uygulamanın *Web. config* dosyası dışında depolanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="8b067-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="8b067-589">Daha fazla bilgi ve IIS URL yeniden yazma modülü kurallarının örnekleri için bkz. [Using URL yeniden yazma modülü 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) ve [URL yeniden yazma modülü yapılandırma başvurusu](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="8b067-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="8b067-590"><xref:System.IO.StreamReader> , *Iisurlyeniden yazma. xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="8b067-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="8b067-591">Örnek uygulama, ' den ' `/iis-rules-rewrite/(.*)` e `/rewritten?id=$1`olan istekleri yeniden yazar.</span><span class="sxs-lookup"><span data-stu-id="8b067-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="8b067-592">Yanıt, istemciye *200-ok* durum kodu ile gönderilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="8b067-593">Özgün Istek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="8b067-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="8b067-595">Uygulamanızı istenmeyen yollarla etkileyebilecek sunucu düzeyi kurallara sahip etkin bir IIS yeniden yazma modülünüzün olması halinde, bir uygulama için IIS yeniden yazma modülünü devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="8b067-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="8b067-596">Daha fazla bilgi için bkz. [IIS modüllerini devre dışı bırakma](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="8b067-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="8b067-597">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="8b067-597">Unsupported features</span></span>

<span data-ttu-id="8b067-598">ASP.NET Core 2. x ile yayınlanan ara yazılım, aşağıdaki IIS URL yeniden yazma modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="8b067-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="8b067-599">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="8b067-599">Outbound Rules</span></span>
* <span data-ttu-id="8b067-600">Özel sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="8b067-600">Custom Server Variables</span></span>
* <span data-ttu-id="8b067-601">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="8b067-601">Wildcards</span></span>
* <span data-ttu-id="8b067-602">LogRewrittenUrl 'Si</span><span class="sxs-lookup"><span data-stu-id="8b067-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="8b067-603">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="8b067-603">Supported server variables</span></span>

<span data-ttu-id="8b067-604">Ara yazılım aşağıdaki IIS URL yeniden yazma modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="8b067-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="8b067-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="8b067-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="8b067-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="8b067-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="8b067-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="8b067-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="8b067-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="8b067-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="8b067-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="8b067-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="8b067-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="8b067-610">HTTP_HOST</span></span>
* <span data-ttu-id="8b067-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="8b067-611">HTTP_REFERER</span></span>
* <span data-ttu-id="8b067-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="8b067-612">HTTP_URL</span></span>
* <span data-ttu-id="8b067-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="8b067-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="8b067-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="8b067-614">HTTPS</span></span>
* <span data-ttu-id="8b067-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="8b067-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="8b067-616">QUERY_STRING</span></span>
* <span data-ttu-id="8b067-617">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="8b067-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="8b067-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="8b067-618">REMOTE_PORT</span></span>
* <span data-ttu-id="8b067-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="8b067-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="8b067-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="8b067-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="8b067-621">Ayrıca bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> ile elde edebilirsiniz <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span><span class="sxs-lookup"><span data-stu-id="8b067-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="8b067-622">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="8b067-623">Yeniden yazma kuralları dosyalarınızın sağladığınız yoldaki sunucuya dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="8b067-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="8b067-624">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="8b067-624">Method-based rule</span></span>

<span data-ttu-id="8b067-625">Bir <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="8b067-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="8b067-626">`Add`, metodunda <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>kullanım <xref:Microsoft.AspNetCore.Http.HttpContext> için kullanılabilir hale getiren öğesini gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="8b067-627">[Rewritecontext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) , ek ardışık düzen işlemenin nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="8b067-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="8b067-628">Değeri aşağıdaki tabloda açıklanan <xref:Microsoft.AspNetCore.Rewrite.RuleResult> alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="8b067-629">Eylem</span><span class="sxs-lookup"><span data-stu-id="8b067-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="8b067-630">`RuleResult.ContinueRules`varsayılanını</span><span class="sxs-lookup"><span data-stu-id="8b067-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="8b067-631">Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="8b067-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="8b067-632">Kuralları uygulamayı durdurun ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="8b067-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="8b067-633">Kuralları uygulamayı durdurun ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="8b067-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="8b067-634">Örnek uygulama, *. xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="8b067-635">İçin `/file.xml`bir istek yapılırsa, istek öğesine `/xmlfiles/file.xml`yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="8b067-636">Durum kodu *301*olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8b067-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="8b067-637">Tarayıcı */xmlfiles/File.xml*için yeni bir istek yaptığında, statik dosya ara yazılımı dosyayı *Wwwroot/xmlfiles* klasöründen istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="8b067-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="8b067-638">Yeniden yönlendirme için, yanıtın durum kodunu açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="8b067-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="8b067-639">Aksi takdirde, *200-ok* durum kodu döndürülür ve yeniden yönlendirme istemcide gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="8b067-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="8b067-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b067-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="8b067-641">Bu yaklaşım ayrıca istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="8b067-642">Örnek uygulama, *dosya. txt* metin dosyasına *Wwwroot* klasöründen hizmeti sağlamak için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="8b067-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="8b067-643">Statik dosya ara yazılımı, güncelleştirilmiş istek yoluna göre dosyayı sunar:</span><span class="sxs-lookup"><span data-stu-id="8b067-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="8b067-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="8b067-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="8b067-645">Irule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="8b067-645">IRule-based rule</span></span>

<span data-ttu-id="8b067-646">Arabirimini uygulayan bir sınıfta kural mantığını kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> <xref:Microsoft.AspNetCore.Rewrite.IRule></span><span class="sxs-lookup"><span data-stu-id="8b067-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="8b067-647">`IRule`Yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="8b067-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="8b067-648">Uygulama sınıfınız, <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> yöntemi için parametreleri geçirebilmeniz için bir Oluşturucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="8b067-649">`extension` Ve `newPath` için örnek uygulamadaki parametrelerin değerleri, çeşitli koşullara uyacak şekilde denetlenir.</span><span class="sxs-lookup"><span data-stu-id="8b067-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="8b067-650">`extension` Bir değer içermeli ve değer *. png*, *. jpg*veya *. gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="8b067-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="8b067-651">Geçerli `newPath` değilse, bir <xref:System.ArgumentException> oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="8b067-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="8b067-652">*Image. png*için bir istek yapılırsa, istek öğesine `/png-images/image.png`yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="8b067-653">*Image. jpg*için bir istek yapılırsa, istek öğesine `/jpg-images/image.jpg`yeniden yönlendirilir.</span><span class="sxs-lookup"><span data-stu-id="8b067-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="8b067-654">Durum kodu 301 olarak ayarlanır ve *kalıcı olarak taşınır*ve `context.Result` kuralları işlemeyi durdur ve yanıtı gönder olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="8b067-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="8b067-655">Özgün Istek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="8b067-655">Original Request: `/image.png`</span></span>

![Görüntü. png için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="8b067-657">Özgün Istek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="8b067-657">Original Request: `/image.jpg`</span></span>

![Image. jpg için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="8b067-659">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="8b067-659">Regex examples</span></span>

| <span data-ttu-id="8b067-660">Hedef</span><span class="sxs-lookup"><span data-stu-id="8b067-660">Goal</span></span> | <span data-ttu-id="8b067-661">Regex dize &</span><span class="sxs-lookup"><span data-stu-id="8b067-661">Regex String &</span></span><br><span data-ttu-id="8b067-662">Match örneği</span><span class="sxs-lookup"><span data-stu-id="8b067-662">Match Example</span></span> | <span data-ttu-id="8b067-663">Değiştirme dizesi &</span><span class="sxs-lookup"><span data-stu-id="8b067-663">Replacement String &</span></span><br><span data-ttu-id="8b067-664">Çıkış örneği</span><span class="sxs-lookup"><span data-stu-id="8b067-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="8b067-665">Yolu QueryString 'e yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="8b067-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="8b067-666">Eğik çizgiyi çıkar</span><span class="sxs-lookup"><span data-stu-id="8b067-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="8b067-667">Sondaki eğik çizgiyi zorla</span><span class="sxs-lookup"><span data-stu-id="8b067-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="8b067-668">Belirli istekleri yeniden yazmayı önleyin</span><span class="sxs-lookup"><span data-stu-id="8b067-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="8b067-669">`^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="8b067-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="8b067-670">Yes`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="8b067-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="8b067-671">Eşleşen`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="8b067-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="8b067-672">URL segmentlerini yeniden Düzenle</span><span class="sxs-lookup"><span data-stu-id="8b067-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="8b067-673">URL segmentini değiştirme</span><span class="sxs-lookup"><span data-stu-id="8b067-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="8b067-674">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="8b067-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="8b067-675">.NET içinde normal ifadeler</span><span class="sxs-lookup"><span data-stu-id="8b067-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="8b067-676">Normal ifade dili-hızlı başvuru</span><span class="sxs-lookup"><span data-stu-id="8b067-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="8b067-677">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="8b067-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="8b067-678">URL yeniden yazma modülünü kullanma 2,0 (IIS için)</span><span class="sxs-lookup"><span data-stu-id="8b067-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="8b067-679">URL yeniden yazma modülü yapılandırma başvurusu</span><span class="sxs-lookup"><span data-stu-id="8b067-679">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="8b067-680">IIS URL yeniden yazma modülü Forumu</span><span class="sxs-lookup"><span data-stu-id="8b067-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="8b067-681">Basit URL yapısını saklama</span><span class="sxs-lookup"><span data-stu-id="8b067-681">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="8b067-682">10 URL yeniden yazma Ipuçları ve püf noktaları</span><span class="sxs-lookup"><span data-stu-id="8b067-682">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="8b067-683">Eğik çizgi veya eğik çizgi</span><span class="sxs-lookup"><span data-stu-id="8b067-683">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
