---
title: ASP.NET Core içinde URL yeniden yazma ara yazılımı
author: rick-anderson
description: ASP.NET Core uygulamalarında URL yeniden yazma ve URL yeniden yazma ara yazılımı ile yeniden yönlendirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
no-loc:
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
uid: fundamentals/url-rewriting
ms.openlocfilehash: a1d31428945adade6748185c17d42ef60a61b5dc
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88631712"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="1e7e6-103">ASP.NET Core içinde URL yeniden yazma ara yazılımı</span><span class="sxs-lookup"><span data-stu-id="1e7e6-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="1e7e6-104">X [MIKAEL Mengistu](https://github.com/mikaelm12) tarafından</span><span class="sxs-lookup"><span data-stu-id="1e7e6-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1e7e6-105">Bu belgede, ASP.NET Core uygulamalarında URL yeniden yazma ara yazılımı kullanma yönergeleriyle birlikte URL yeniden yazma tanıtılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="1e7e6-106">URL yeniden yazma, istek URL 'Lerini bir veya daha fazla önceden tanımlanmış kurala göre değiştirme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="1e7e6-107">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmaması için kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="1e7e6-108">URL yeniden yazma işlemi birkaç senaryoda yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="1e7e6-109">Sunucu kaynaklarını geçici olarak veya kalıcı olarak taşıyın veya değiştirin ve bu kaynakların kararlı konum belirleyicilerinin bakımını yapın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="1e7e6-110">İstek işlemeyi farklı uygulamalar arasında veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="1e7e6-111">Gelen isteklerde URL segmentlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="1e7e6-112">Arama motoru Iyileştirmesi (SEO) için genel URL 'Leri iyileştirin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="1e7e6-113">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmeye yardımcı olmak için kolay genel URL 'Lerin kullanılmasına izin verme.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="1e7e6-114">Güvensiz istekleri güvenli uç noktalara yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="1e7e6-115">Bir dış sitenin varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı Hotlink 'i engelleyin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="1e7e6-116">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="1e7e6-117">Uygun yerlerde kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="1e7e6-118">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e7e6-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="1e7e6-119">URL yeniden yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="1e7e6-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="1e7e6-120">*URL yeniden yönlendirme* ve *URL yeniden yazma* arasındaki ifade farkı daha hafif ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="1e7e6-121">ASP.NET Core URL yeniden yazma ara yazılımı her ikisine de ihtiyacı verebilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="1e7e6-122">*URL yeniden yönlendirme* , istemcinin, ilk olarak istenen istemciden farklı bir adresteki kaynağa erişmesi için bir istemci tarafı işlemi içerir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="1e7e6-123">Bu, sunucuya gidiş dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-123">This requires a round trip to the server.</span></span> <span data-ttu-id="1e7e6-124">İstemci kaynak için yeni bir istek yaptığında, istemciye döndürülen yeniden yönlendirme URL 'SI tarayıcının adres çubuğunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="1e7e6-125">`/resource`Öğesine *yönlendiriliyorsa* `/different-resource` sunucu, istemcinin, `/different-resource` yeniden yönlendirmenin geçici ya da kalıcı olduğunu belirten bir durum kodu ile kaynağı elde etmesi gerektiğini yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmeti uç noktası, sürüm 1 ' den (v1) sunucudaki sürüm 2 ' ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="1e7e6-131">İstekleri farklı bir URL 'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yeniden yönlendirmenin kalıcı mi yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="1e7e6-132">*301-taşınan kalıcı* durum kodu, kaynağın yeni, kalıcı bir URL olduğu ve istemciye, gelecekteki tüm isteklerin kaynak için tüm istekleri yeni URL 'yi kullanması gerektiğini bildirmek istediğinizde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="1e7e6-133">*İstemci, 301 durum kodu alındığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="1e7e6-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="1e7e6-134">*302-bulunan* durum kodu, yeniden yönlendirmenin geçici ya da genellikle değişikliğe tabi olduğu durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="1e7e6-135">302 durum kodu, istemcinin URL 'YI depolayamadığını ve gelecekte kullanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="1e7e6-136">Durum kodları hakkında daha fazla bilgi için bkz. [RFC 2616: durum kodu tanımları](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="1e7e6-137">*URL yeniden yazma* , istenen istemciden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="1e7e6-138">URL yeniden yazma, sunucuya gidiş dönüş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="1e7e6-139">Yeniden yazan URL istemciye döndürülmüyor ve tarayıcının adres çubuğunda görünmüyor.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="1e7e6-140">`/resource`Öğesine *rewritten* `/different-resource` geri dönerse, sunucu, kaynağını *dahili olarak* getirir ve döndürür `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="1e7e6-141">İstemci, yeniden yazan URL 'de kaynağı alabiliyor olsa da, istemci isteği yaptığında ve yanıtı aldığında kaynağın yeniden yazan URL 'de bulunduğunu bilgilendirmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Sunucu üzerinde sürüm 1 ' den (v1) sürüm 2 ' ye (v2) bir WebAPI hizmet uç noktası değiştirilmiştir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="1e7e6-146">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="1e7e6-146">URL rewriting sample app</span></span>

<span data-ttu-id="1e7e6-147">[Örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)birlikte YENIDEN yazma URL 'sinin özelliklerini inceleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="1e7e6-148">Uygulama yeniden yönlendirme ve yeniden yazma kuralları uygular ve birkaç senaryo için yeniden yönlendirilen veya yeniden yazan URL 'YI gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="1e7e6-149">URL yeniden yazma ara yazılımı ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="1e7e6-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="1e7e6-150">Aşağıdaki yaklaşımlardan birini kullandığımmdan URL yeniden yazma ara yazılımı kullanın:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="1e7e6-151">Windows Server 'da IIS ile URL yeniden yazma modülü</span><span class="sxs-lookup"><span data-stu-id="1e7e6-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="1e7e6-152">Apache Server 'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="1e7e6-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="1e7e6-153">NGINX üzerinde URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="1e7e6-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="1e7e6-154">Ayrıca, uygulama [HTTP.sys sunucuda](xref:fundamentals/servers/httpsys) barındırıldığı zaman ara yazılımı kullanın (daha önce webListener olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="1e7e6-155">IIS, Apache ve NGINX 'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="1e7e6-156">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="1e7e6-157">Sunucu modüllerinin bazı özellikleri, `IsFile` `IsDirectory` IIS yeniden yazma modülünün ve kısıtlamaları gibi ASP.NET Core projelerle birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="1e7e6-158">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="1e7e6-159">Ara yazılım performansı büyük olasılıkla modüllerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="1e7e6-160">Sınama, performansı en iyi şekilde düşürür veya performans düşüklüğü göz ardı edilebilir olduğundan emin olmanın tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="1e7e6-161">Paket</span><span class="sxs-lookup"><span data-stu-id="1e7e6-161">Package</span></span>

<span data-ttu-id="1e7e6-162">URL yeniden yazma ara yazılımı, ASP.NET Core uygulamalarında örtük olarak bulunan [Microsoft. AspNetCore. yeniden yazma](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketi tarafından sağlanır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="1e7e6-163">Uzantı ve Seçenekler</span><span class="sxs-lookup"><span data-stu-id="1e7e6-163">Extension and options</span></span>

<span data-ttu-id="1e7e6-164">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriyle [Rewriteoptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir ÖRNEĞINI oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="1e7e6-165">Birden çok kuralı, işlenmeyi istediğiniz sırada zincirle.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="1e7e6-166">, `RewriteOptions` İle istek ardışık düzenine eklendikçe, bu URL 'ye yeniden yazma ara yazılımı ile geçirilir <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="1e7e6-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="1e7e6-167">Www olmayan www 'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-167">Redirect non-www to www</span></span>

<span data-ttu-id="1e7e6-168">Üç seçenek, uygulamanın istek olmayan istekleri yeniden yönlendirmesine izin verir `www` `www` :</span><span class="sxs-lookup"><span data-stu-id="1e7e6-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="1e7e6-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: İstek değilse, istek alt etki alanına kalıcı olarak yeniden yönlendirin `www` `www` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="1e7e6-170">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="1e7e6-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: `www` Gelen istek değilse isteği alt etki alanına yeniden yönlendirin `www` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="1e7e6-172">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="1e7e6-173">Aşırı yükleme, yanıt için durum kodu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="1e7e6-174"><xref:Microsoft.AspNetCore.Http.StatusCodes>Bir durum kodu ataması için sınıfın bir alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="1e7e6-175">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-175">URL redirect</span></span>

<span data-ttu-id="1e7e6-176"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>İstekleri yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="1e7e6-177">İlk parametre gelen URL 'nin yolu ile eşleşen Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="1e7e6-178">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e7e6-179">Varsa, üçüncü parametre durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="1e7e6-180">Durum kodunu belirtmezseniz, durum kodu varsayılan olarak *302-bulunur*; bu da kaynağın geçici olarak taşındığını veya değiştirildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="1e7e6-181">Geliştirici araçları etkinleştirilmiş bir tarayıcıda, örnek uygulamaya yol ile bir istek oluşturun `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="1e7e6-182">Regex, üzerindeki istek yoluyla eşleşir `redirect-rule/(.*)` ve yol ile değiştirilmiştir `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="1e7e6-183">Yeniden yönlendirme URL 'SI, *302 tarafından bulunan* bir durum kodu ile istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="1e7e6-184">Tarayıcı, tarayıcının adres çubuğunda görüntülenen yeniden yönlendirme URL 'SI üzerinde yeni bir istek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="1e7e6-185">Örnek uygulamadaki hiçbir kural, yeniden yönlendirme URL 'SI üzerinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="1e7e6-186">İkinci istek uygulamadan *200-Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="1e7e6-187">Yanıtın gövdesi, yeniden yönlendirme URL 'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="1e7e6-188">Bir URL *yeniden yönlendirildiğinde*sunucuya gidiş dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="1e7e6-189">Yeniden yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="1e7e6-190">Yeniden yönlendirme kuralları, bir yeniden yönlendirmeden sonra dahil olmak üzere, uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="1e7e6-191">Yanlışlıkla *sonsuz yeniden yönlendirmeler döngüsü*oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="1e7e6-192">Özgün Istek: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="1e7e6-194">Parantez içinde yer alan ifadenin kısmına bir *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="1e7e6-195">İfadenin nokta ( `.` ), *herhangi bir karakterle eşleşir*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="1e7e6-196">Yıldız işareti ( `*` ) *, önceki karakterle sıfır veya daha fazla kez eşleşme*gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="1e7e6-197">Bu nedenle, URL 'nin son iki yol kesimi, `1234/5678` yakalama grubu tarafından yakalanır `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="1e7e6-198">Sonrasında istek URL 'sinde sağladığınız herhangi bir değer `redirect-rule/` Bu tek yakalama grubu tarafından yakalandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="1e7e6-199">Değiştirme dizesinde, yakalanan gruplar, dolar işareti ( `$` ) ve ardından yakalamanın sıra numarası ile birlikte dizeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="1e7e6-200">İlk yakalama grubu değeri ile elde edilir `$1` , ikincisi ile `$2` ve normal Regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="1e7e6-201">Örnek uygulamadaki yeniden yönlendirme kuralı Regex bölümünde yalnızca bir tane yakalanan grup bulunur, bu nedenle değiştirme dizesinde yalnızca bir tane eklenmiş grup vardır `$1` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="1e7e6-202">Kural uygulandığında, URL olur `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="1e7e6-203">Güvenli bir uç noktaya URL yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="1e7e6-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="1e7e6-204"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>Http ISTEKLERINI https protokolünü kullanarak aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="1e7e6-205">Durum kodu sağlanmazsa, ara yazılım varsayılan olarak *302-bulunur*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="1e7e6-206">Bağlantı noktası sağlanmazsa:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="1e7e6-207">Ara yazılım varsayılan olarak olur `null` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="1e7e6-208">Şema `https` (https Protokolü) olarak değişir ve istemci, 443 numaralı bağlantı noktasında kaynağa erişir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="1e7e6-209">Aşağıdaki örnek, durum kodunun *301-kalıcı olarak taşınacağını* ve bağlantı noktasını 5001 olarak nasıl değiştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="1e7e6-210"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>Güvenli olmayan istekleri, bağlantı noktası 443 üzerinde GÜVENLI https Protokolü ile aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="1e7e6-211">Ara yazılım durum kodunu 301 olarak ayarlar ve *kalıcı olarak taşınır*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="1e7e6-212">Ek yeniden yönlendirme kuralları gereksinimi olmadan güvenli bir uç noktaya yönlendirilirken, HTTPS yeniden yönlendirme ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1e7e6-213">Daha fazla bilgi için bkz. [https 'Yi zorla](xref:security/enforcing-ssl#require-https) konusu.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="1e7e6-214">Örnek uygulama, veya kullanımını gösterme yeteneğine sahiptir `AddRedirectToHttps` `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="1e7e6-215">Uzantı yöntemini öğesine ekleyin `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="1e7e6-216">Herhangi bir URL 'de uygulamaya güvenli olmayan bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="1e7e6-217">Otomatik olarak imzalanan sertifikanın güvenilmeyen tarayıcı güvenlik uyarısını kapatın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="1e7e6-218">Kullanarak özgün Istek `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="1e7e6-220">Kullanarak özgün Istek `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="1e7e6-222">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="1e7e6-222">URL rewrite</span></span>

<span data-ttu-id="1e7e6-223"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>URL 'leri yeniden yazma kuralı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="1e7e6-224">İlk parametre gelen URL yolundaki eşleşme için Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="1e7e6-225">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e7e6-226">Üçüncü parametresi, `skipRemainingRules: {true|false}` geçerli kural uygulanmışsa ek yeniden yazma kurallarının atlanıp atlanmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="1e7e6-227">Özgün Istek: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="1e7e6-229">İfadenin başındaki simgeyi seçtiğinizde ( `^` ), eşleşmesinin URL yolunun başlangıcında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="1e7e6-230">Önceki örnekte, yeniden yönlendirme kuralıyla, Regex başlangıcında `redirect-rule/(.*)` simgeyi seçtiğinizde ( `^` ) yoktur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="1e7e6-231">Bu nedenle, `redirect-rule/` başarılı bir eşleşme için herhangi bir karakterden önce yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="1e7e6-232">Yol</span><span class="sxs-lookup"><span data-stu-id="1e7e6-232">Path</span></span>                               | <span data-ttu-id="1e7e6-233">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="1e7e6-234">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="1e7e6-235">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="1e7e6-236">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-236">Yes</span></span>   |

<span data-ttu-id="1e7e6-237">Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)` yalnızca ile başlarsa yollarla eşleşir `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="1e7e6-238">Aşağıdaki tabloda, eşleşen farkı aklınızda.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="1e7e6-239">Yol</span><span class="sxs-lookup"><span data-stu-id="1e7e6-239">Path</span></span>                              | <span data-ttu-id="1e7e6-240">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="1e7e6-241">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="1e7e6-242">Hayır</span><span class="sxs-lookup"><span data-stu-id="1e7e6-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="1e7e6-243">Hayır</span><span class="sxs-lookup"><span data-stu-id="1e7e6-243">No</span></span>    |

<span data-ttu-id="1e7e6-244">`^rewrite-rule/`İfadenin bölümünü takip eden iki yakalama grubu vardır `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="1e7e6-245">`\d`Belirtir *bir sayıyla (sayı) eşleşir*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="1e7e6-246">Artı işareti ( `+` ) *bir veya daha fazla önceki karakterden eşleşiyor*demektir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="1e7e6-247">Bu nedenle, URL bir sayı içermeli ve ardından İleri eğik çizgi ve ardından başka bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="1e7e6-248">Bu yakalama grupları, ve olarak yeniden yazan URL 'sine `$1` eklenir `$2` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="1e7e6-249">Yeniden yazma kuralı değiştirme dizesi yakalanan grupları sorgu dizesine koyar.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="1e7e6-250">İstenen yolu, `/rewrite-rule/1234/5678` kaynağı elde etmek için yeniden yazılır `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="1e7e6-251">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazdığınızda korunur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="1e7e6-252">Kaynağı almak için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="1e7e6-253">Kaynak varsa, bu, alınır ve istemciye *200-ok* durum kodu ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="1e7e6-254">İstemci yeniden yönlendirmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="1e7e6-255">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamaz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1e7e6-256">`skipRemainingRules: true`Eşleşen kuralların hesaplama maliyeti ve uygulama yanıt süresini arttığı için mümkün olan her durumda kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="1e7e6-257">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="1e7e6-258">En sık eşleşen kuraldan en az sıklıkta eşleşen kurala göre yeniden yazma kuralları.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="1e7e6-259">Bir eşleşme gerçekleştiğinde ve ek kural işleme gerekli olmadığında kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="1e7e6-260">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1e7e6-260">Apache mod_rewrite</span></span>

<span data-ttu-id="1e7e6-261">İle Apache mod_rewrite kuralları uygulayın <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="1e7e6-262">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e7e6-263">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için bkz. [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="1e7e6-264">, <xref:System.IO.StreamReader> *ApacheModRewrite.txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="1e7e6-265">Örnek uygulama, istekleri ' den ' e yeniden yönlendirir `/apache-mod-rules-redirect/(.\*)` `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="1e7e6-266">Yanıt durum kodu *302-bulundu*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="1e7e6-267">Özgün Istek: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="1e7e6-269">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="1e7e6-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="1e7e6-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e7e6-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e7e6-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e7e6-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e7e6-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e7e6-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="1e7e6-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="1e7e6-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e7e6-275">HTTP_HOST</span></span>
* <span data-ttu-id="1e7e6-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e7e6-276">HTTP_REFERER</span></span>
* <span data-ttu-id="1e7e6-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e7e6-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e7e6-278">HTTPS</span></span>
* <span data-ttu-id="1e7e6-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="1e7e6-279">IPV6</span></span>
* <span data-ttu-id="1e7e6-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e7e6-280">QUERY_STRING</span></span>
* <span data-ttu-id="1e7e6-281">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e7e6-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-282">REMOTE_PORT</span></span>
* <span data-ttu-id="1e7e6-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e7e6-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="1e7e6-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="1e7e6-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="1e7e6-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e7e6-286">REQUEST_URI</span></span>
* <span data-ttu-id="1e7e6-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="1e7e6-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-288">SERVER_ADDR</span></span>
* <span data-ttu-id="1e7e6-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-289">SERVER_PORT</span></span>
* <span data-ttu-id="1e7e6-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="1e7e6-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="1e7e6-291">TIME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-291">TIME</span></span>
* <span data-ttu-id="1e7e6-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="1e7e6-292">TIME_DAY</span></span>
* <span data-ttu-id="1e7e6-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-293">TIME_HOUR</span></span>
* <span data-ttu-id="1e7e6-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="1e7e6-294">TIME_MIN</span></span>
* <span data-ttu-id="1e7e6-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="1e7e6-295">TIME_MON</span></span>
* <span data-ttu-id="1e7e6-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="1e7e6-296">TIME_SEC</span></span>
* <span data-ttu-id="1e7e6-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="1e7e6-297">TIME_WDAY</span></span>
* <span data-ttu-id="1e7e6-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="1e7e6-299">IIS URL yeniden yazma modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="1e7e6-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="1e7e6-300">IIS URL yeniden yazma modülü için geçerli olan kural kümesini kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="1e7e6-301">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e7e6-302">Windows Server IIS üzerinde çalışırken uygulamanın *web.config* dosyasını kullanmak için ara yazılımı yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="1e7e6-303">IIS ile, IIS yeniden yazma modülüyle çakışmalardan kaçınmak için bu kuralların uygulamanın *web.config* dosyası dışında depolanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="1e7e6-304">Daha fazla bilgi ve IIS URL yeniden yazma modülü kurallarının örnekleri için bkz. [Using URL yeniden yazma modülü 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) ve [URL yeniden yazma modülü yapılandırma başvurusu](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="1e7e6-305">, <xref:System.IO.StreamReader> *IISUrlRewrite.xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="1e7e6-306">Örnek uygulama, ' den ' e olan istekleri yeniden yazar `/iis-rules-rewrite/(.*)` `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="1e7e6-307">Yanıt, istemciye *200-ok* durum kodu ile gönderilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="1e7e6-308">Özgün Istek: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="1e7e6-310">Uygulamanızı istenmeyen yollarla etkileyebilecek sunucu düzeyi kurallara sahip etkin bir IIS yeniden yazma modülünüzün olması halinde, bir uygulama için IIS yeniden yazma modülünü devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="1e7e6-311">Daha fazla bilgi için bkz. [IIS modüllerini devre dışı bırakma](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="1e7e6-312">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="1e7e6-312">Unsupported features</span></span>

<span data-ttu-id="1e7e6-313">ASP.NET Core 2. x ile yayınlanan ara yazılım, aşağıdaki IIS URL yeniden yazma modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="1e7e6-314">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="1e7e6-314">Outbound Rules</span></span>
* <span data-ttu-id="1e7e6-315">Özel sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="1e7e6-315">Custom Server Variables</span></span>
* <span data-ttu-id="1e7e6-316">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="1e7e6-316">Wildcards</span></span>
* <span data-ttu-id="1e7e6-317">LogRewrittenUrl 'Si</span><span class="sxs-lookup"><span data-stu-id="1e7e6-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="1e7e6-318">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="1e7e6-318">Supported server variables</span></span>

<span data-ttu-id="1e7e6-319">Ara yazılım aşağıdaki IIS URL yeniden yazma modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="1e7e6-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="1e7e6-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="1e7e6-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="1e7e6-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="1e7e6-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e7e6-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e7e6-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e7e6-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e7e6-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e7e6-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e7e6-325">HTTP_HOST</span></span>
* <span data-ttu-id="1e7e6-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e7e6-326">HTTP_REFERER</span></span>
* <span data-ttu-id="1e7e6-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="1e7e6-327">HTTP_URL</span></span>
* <span data-ttu-id="1e7e6-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e7e6-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e7e6-329">HTTPS</span></span>
* <span data-ttu-id="1e7e6-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="1e7e6-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e7e6-331">QUERY_STRING</span></span>
* <span data-ttu-id="1e7e6-332">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e7e6-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-333">REMOTE_PORT</span></span>
* <span data-ttu-id="1e7e6-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e7e6-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e7e6-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="1e7e6-336">Ayrıca bir ile elde edebilirsiniz <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="1e7e6-337">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="1e7e6-338">Yeniden yazma kuralları dosyalarınızın sağladığınız yoldaki sunucuya dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="1e7e6-339">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="1e7e6-339">Method-based rule</span></span>

<span data-ttu-id="1e7e6-340"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Bir yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="1e7e6-341">`Add` , <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> metodunda kullanım için kullanılabilir hale getiren öğesini gösterir <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="1e7e6-342">[Rewritecontext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) , ek ardışık düzen işlemenin nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="1e7e6-343">Değeri <xref:Microsoft.AspNetCore.Rewrite.RuleResult> Aşağıdaki tabloda açıklanan alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="1e7e6-344">Bağlam sonucunu yeniden yaz</span><span class="sxs-lookup"><span data-stu-id="1e7e6-344">Rewrite context result</span></span>               | <span data-ttu-id="1e7e6-345">Eylem</span><span class="sxs-lookup"><span data-stu-id="1e7e6-345">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="1e7e6-346">`RuleResult.ContinueRules` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="1e7e6-346">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="1e7e6-347">Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-347">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="1e7e6-348">Kuralları uygulamayı durdurun ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-348">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="1e7e6-349">Kuralları uygulamayı durdurun ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-349">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="1e7e6-350">Örnek uygulama, *. xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-350">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="1e7e6-351">İçin bir istek yapılırsa `/file.xml` , istek öğesine yeniden yönlendirilir `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-351">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="1e7e6-352">Durum kodu *301*olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-352">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="1e7e6-353">Tarayıcı */xmlfiles/file.xml*için yeni bir istek yaptığında, statik dosya ara yazılımı dosyayı *Wwwroot/xmlfiles* klasöründen istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-353">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="1e7e6-354">Yeniden yönlendirme için, yanıtın durum kodunu açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-354">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="1e7e6-355">Aksi takdirde, *200-ok* durum kodu döndürülür ve yeniden yönlendirme istemcide gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-355">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="1e7e6-356">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-356">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="1e7e6-357">Bu yaklaşım ayrıca istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-357">This approach can also rewrite requests.</span></span> <span data-ttu-id="1e7e6-358">Örnek uygulama, herhangi bir metin dosyası isteğinin yolunu *Wwwroot* klasöründen *file.txt* metin dosyasına sunacak şekilde yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-358">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="1e7e6-359">Statik dosya ara yazılımı, güncelleştirilmiş istek yoluna göre dosyayı sunar:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-359">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="1e7e6-360">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-360">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="1e7e6-361">Irule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="1e7e6-361">IRule-based rule</span></span>

<span data-ttu-id="1e7e6-362"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Arabirimini uygulayan bir sınıfta kural mantığını kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IRule> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-362">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="1e7e6-363">`IRule` Yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-363">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="1e7e6-364">Uygulama sınıfınız, yöntemi için parametreleri geçirebilmeniz için bir Oluşturucu içerebilir <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-364">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="1e7e6-365">Ve için örnek uygulamadaki parametrelerin değerleri, `extension` `newPath` çeşitli koşullara uyacak şekilde denetlenir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-365">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="1e7e6-366">`extension`Bir değer içermeli ve değer *. png*, *. jpg*veya *. gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-366">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="1e7e6-367">`newPath`Geçerli değilse, bir oluşturulur <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-367">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="1e7e6-368">*image.png*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-368">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="1e7e6-369">*image.jpg*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-369">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="1e7e6-370">Durum kodu 301 olarak ayarlanır ve *kalıcı olarak taşınır*ve `context.Result` kuralları işlemeyi durdur ve yanıtı gönder olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-370">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="1e7e6-371">Özgün Istek: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-371">Original Request: `/image.png`</span></span>

![image.png için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="1e7e6-373">Özgün Istek: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-373">Original Request: `/image.jpg`</span></span>

![image.jpg için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="1e7e6-375">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="1e7e6-375">Regex examples</span></span>

| <span data-ttu-id="1e7e6-376">Hedef</span><span class="sxs-lookup"><span data-stu-id="1e7e6-376">Goal</span></span> | <span data-ttu-id="1e7e6-377">Regex dize &</span><span class="sxs-lookup"><span data-stu-id="1e7e6-377">Regex String &</span></span><br><span data-ttu-id="1e7e6-378">Match örneği</span><span class="sxs-lookup"><span data-stu-id="1e7e6-378">Match Example</span></span> | <span data-ttu-id="1e7e6-379">Değiştirme dizesi &</span><span class="sxs-lookup"><span data-stu-id="1e7e6-379">Replacement String &</span></span><br><span data-ttu-id="1e7e6-380">Çıkış örneği</span><span class="sxs-lookup"><span data-stu-id="1e7e6-380">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="1e7e6-381">Yolu QueryString 'e yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="1e7e6-381">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="1e7e6-382">Eğik çizgiyi çıkar</span><span class="sxs-lookup"><span data-stu-id="1e7e6-382">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="1e7e6-383">Sondaki eğik çizgiyi zorla</span><span class="sxs-lookup"><span data-stu-id="1e7e6-383">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="1e7e6-384">Belirli istekleri yeniden yazmayı önleyin</span><span class="sxs-lookup"><span data-stu-id="1e7e6-384">Avoid rewriting specific requests</span></span> | <span data-ttu-id="1e7e6-385">`^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-385">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="1e7e6-386">Yes `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-386">Yes: `/resource.htm`</span></span><br><span data-ttu-id="1e7e6-387">Eşleşen `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-387">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="1e7e6-388">URL segmentlerini yeniden Düzenle</span><span class="sxs-lookup"><span data-stu-id="1e7e6-388">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="1e7e6-389">URL segmentini değiştirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-389">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1e7e6-390">Bu belgede, ASP.NET Core uygulamalarında URL yeniden yazma ara yazılımı kullanma yönergeleriyle birlikte URL yeniden yazma tanıtılmaktadır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-390">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="1e7e6-391">URL yeniden yazma, istek URL 'Lerini bir veya daha fazla önceden tanımlanmış kurala göre değiştirme işlemidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-391">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="1e7e6-392">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmaması için kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-392">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="1e7e6-393">URL yeniden yazma işlemi birkaç senaryoda yararlı olur:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-393">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="1e7e6-394">Sunucu kaynaklarını geçici olarak veya kalıcı olarak taşıyın veya değiştirin ve bu kaynakların kararlı konum belirleyicilerinin bakımını yapın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-394">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="1e7e6-395">İstek işlemeyi farklı uygulamalar arasında veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-395">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="1e7e6-396">Gelen isteklerde URL segmentlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-396">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="1e7e6-397">Arama motoru Iyileştirmesi (SEO) için genel URL 'Leri iyileştirin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-397">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="1e7e6-398">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmeye yardımcı olmak için kolay genel URL 'Lerin kullanılmasına izin verme.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-398">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="1e7e6-399">Güvensiz istekleri güvenli uç noktalara yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-399">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="1e7e6-400">Bir dış sitenin varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı Hotlink 'i engelleyin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-400">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="1e7e6-401">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-401">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="1e7e6-402">Uygun yerlerde kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-402">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="1e7e6-403">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="1e7e6-403">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="1e7e6-404">URL yeniden yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="1e7e6-404">URL redirect and URL rewrite</span></span>

<span data-ttu-id="1e7e6-405">*URL yeniden yönlendirme* ve *URL yeniden yazma* arasındaki ifade farkı daha hafif ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-405">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="1e7e6-406">ASP.NET Core URL yeniden yazma ara yazılımı her ikisine de ihtiyacı verebilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-406">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="1e7e6-407">*URL yeniden yönlendirme* , istemcinin, ilk olarak istenen istemciden farklı bir adresteki kaynağa erişmesi için bir istemci tarafı işlemi içerir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-407">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="1e7e6-408">Bu, sunucuya gidiş dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-408">This requires a round trip to the server.</span></span> <span data-ttu-id="1e7e6-409">İstemci kaynak için yeni bir istek yaptığında, istemciye döndürülen yeniden yönlendirme URL 'SI tarayıcının adres çubuğunda görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-409">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="1e7e6-410">`/resource`Öğesine *yönlendiriliyorsa* `/different-resource` sunucu, istemcinin, `/different-resource` yeniden yönlendirmenin geçici ya da kalıcı olduğunu belirten bir durum kodu ile kaynağı elde etmesi gerektiğini yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-410">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmeti uç noktası, sürüm 1 ' den (v1) sunucudaki sürüm 2 ' ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="1e7e6-416">İstekleri farklı bir URL 'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yeniden yönlendirmenin kalıcı mi yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-416">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="1e7e6-417">*301-taşınan kalıcı* durum kodu, kaynağın yeni, kalıcı bir URL olduğu ve istemciye, gelecekteki tüm isteklerin kaynak için tüm istekleri yeni URL 'yi kullanması gerektiğini bildirmek istediğinizde kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-417">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="1e7e6-418">*İstemci, 301 durum kodu alındığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="1e7e6-418">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="1e7e6-419">*302-bulunan* durum kodu, yeniden yönlendirmenin geçici ya da genellikle değişikliğe tabi olduğu durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-419">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="1e7e6-420">302 durum kodu, istemcinin URL 'YI depolayamadığını ve gelecekte kullanacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-420">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="1e7e6-421">Durum kodları hakkında daha fazla bilgi için bkz. [RFC 2616: durum kodu tanımları](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-421">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="1e7e6-422">*URL yeniden yazma* , istenen istemciden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı bir işlemdir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-422">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="1e7e6-423">URL yeniden yazma, sunucuya gidiş dönüş gerektirmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-423">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="1e7e6-424">Yeniden yazan URL istemciye döndürülmüyor ve tarayıcının adres çubuğunda görünmüyor.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-424">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="1e7e6-425">`/resource`Öğesine *rewritten* `/different-resource` geri dönerse, sunucu, kaynağını *dahili olarak* getirir ve döndürür `/different-resource` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-425">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="1e7e6-426">İstemci, yeniden yazan URL 'de kaynağı alabiliyor olsa da, istemci isteği yaptığında ve yanıtı aldığında kaynağın yeniden yazan URL 'de bulunduğunu bilgilendirmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-426">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![Sunucu üzerinde sürüm 1 ' den (v1) sürüm 2 ' ye (v2) bir WebAPI hizmet uç noktası değiştirilmiştir.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="1e7e6-431">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="1e7e6-431">URL rewriting sample app</span></span>

<span data-ttu-id="1e7e6-432">[Örnek uygulamayla](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)birlikte YENIDEN yazma URL 'sinin özelliklerini inceleyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-432">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="1e7e6-433">Uygulama yeniden yönlendirme ve yeniden yazma kuralları uygular ve birkaç senaryo için yeniden yönlendirilen veya yeniden yazan URL 'YI gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-433">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="1e7e6-434">URL yeniden yazma ara yazılımı ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="1e7e6-434">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="1e7e6-435">Aşağıdaki yaklaşımlardan birini kullandığımmdan URL yeniden yazma ara yazılımı kullanın:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-435">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="1e7e6-436">Windows Server 'da IIS ile URL yeniden yazma modülü</span><span class="sxs-lookup"><span data-stu-id="1e7e6-436">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="1e7e6-437">Apache Server 'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="1e7e6-437">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="1e7e6-438">NGINX üzerinde URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="1e7e6-438">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="1e7e6-439">Ayrıca, uygulama [HTTP.sys sunucuda](xref:fundamentals/servers/httpsys) barındırıldığı zaman ara yazılımı kullanın (daha önce webListener olarak adlandırılır).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-439">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="1e7e6-440">IIS, Apache ve NGINX 'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-440">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="1e7e6-441">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-441">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="1e7e6-442">Sunucu modüllerinin bazı özellikleri, `IsFile` `IsDirectory` IIS yeniden yazma modülünün ve kısıtlamaları gibi ASP.NET Core projelerle birlikte çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-442">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="1e7e6-443">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-443">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="1e7e6-444">Ara yazılım performansı büyük olasılıkla modüllerle eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-444">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="1e7e6-445">Sınama, performansı en iyi şekilde düşürür veya performans düşüklüğü göz ardı edilebilir olduğundan emin olmanın tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-445">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="1e7e6-446">Paket</span><span class="sxs-lookup"><span data-stu-id="1e7e6-446">Package</span></span>

<span data-ttu-id="1e7e6-447">Ara yazılımı projenize dahil etmek için, [Microsoft. aspnetcore. yeniden yazma](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketini içeren proje dosyasındaki [Microsoft. Aspnetcore. app metapackage](xref:fundamentals/metapackage-app) öğesine bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-447">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="1e7e6-448">`Microsoft.AspNetCore.App`Metapackage 'i kullanmıyorsanız, pakete bir proje başvurusu ekleyin `Microsoft.AspNetCore.Rewrite` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-448">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="1e7e6-449">Uzantı ve Seçenekler</span><span class="sxs-lookup"><span data-stu-id="1e7e6-449">Extension and options</span></span>

<span data-ttu-id="1e7e6-450">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriyle [Rewriteoptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir ÖRNEĞINI oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-450">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="1e7e6-451">Birden çok kuralı, işlenmeyi istediğiniz sırada zincirle.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-451">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="1e7e6-452">, `RewriteOptions` İle istek ardışık düzenine eklendikçe, bu URL 'ye yeniden yazma ara yazılımı ile geçirilir <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*> :</span><span class="sxs-lookup"><span data-stu-id="1e7e6-452">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="1e7e6-453">Www olmayan www 'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-453">Redirect non-www to www</span></span>

<span data-ttu-id="1e7e6-454">Üç seçenek, uygulamanın istek olmayan istekleri yeniden yönlendirmesine izin verir `www` `www` :</span><span class="sxs-lookup"><span data-stu-id="1e7e6-454">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="1e7e6-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: İstek değilse, istek alt etki alanına kalıcı olarak yeniden yönlendirin `www` `www` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-455"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>: Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="1e7e6-456">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-456">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="1e7e6-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: `www` Gelen istek değilse isteği alt etki alanına yeniden yönlendirin `www` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-457"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>: Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="1e7e6-458">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum kodu ile yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-458">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="1e7e6-459">Aşırı yükleme, yanıt için durum kodu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-459">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="1e7e6-460"><xref:Microsoft.AspNetCore.Http.StatusCodes>Bir durum kodu ataması için sınıfın bir alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-460">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="1e7e6-461">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-461">URL redirect</span></span>

<span data-ttu-id="1e7e6-462"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*>İstekleri yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-462">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="1e7e6-463">İlk parametre gelen URL 'nin yolu ile eşleşen Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-463">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="1e7e6-464">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-464">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e7e6-465">Varsa, üçüncü parametre durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-465">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="1e7e6-466">Durum kodunu belirtmezseniz, durum kodu varsayılan olarak *302-bulunur*; bu da kaynağın geçici olarak taşındığını veya değiştirildiğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-466">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="1e7e6-467">Geliştirici araçları etkinleştirilmiş bir tarayıcıda, örnek uygulamaya yol ile bir istek oluşturun `/redirect-rule/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-467">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="1e7e6-468">Regex, üzerindeki istek yoluyla eşleşir `redirect-rule/(.*)` ve yol ile değiştirilmiştir `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-468">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="1e7e6-469">Yeniden yönlendirme URL 'SI, *302 tarafından bulunan* bir durum kodu ile istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-469">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="1e7e6-470">Tarayıcı, tarayıcının adres çubuğunda görüntülenen yeniden yönlendirme URL 'SI üzerinde yeni bir istek oluşturur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-470">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="1e7e6-471">Örnek uygulamadaki hiçbir kural, yeniden yönlendirme URL 'SI üzerinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-471">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="1e7e6-472">İkinci istek uygulamadan *200-Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-472">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="1e7e6-473">Yanıtın gövdesi, yeniden yönlendirme URL 'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-473">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="1e7e6-474">Bir URL *yeniden yönlendirildiğinde*sunucuya gidiş dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-474">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="1e7e6-475">Yeniden yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-475">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="1e7e6-476">Yeniden yönlendirme kuralları, bir yeniden yönlendirmeden sonra dahil olmak üzere, uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-476">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="1e7e6-477">Yanlışlıkla *sonsuz yeniden yönlendirmeler döngüsü*oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-477">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="1e7e6-478">Özgün Istek: `/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-478">Original Request: `/redirect-rule/1234/5678`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="1e7e6-480">Parantez içinde yer alan ifadenin kısmına bir *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-480">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="1e7e6-481">İfadenin nokta ( `.` ), *herhangi bir karakterle eşleşir*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-481">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="1e7e6-482">Yıldız işareti ( `*` ) *, önceki karakterle sıfır veya daha fazla kez eşleşme*gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-482">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="1e7e6-483">Bu nedenle, URL 'nin son iki yol kesimi, `1234/5678` yakalama grubu tarafından yakalanır `(.*)` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-483">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="1e7e6-484">Sonrasında istek URL 'sinde sağladığınız herhangi bir değer `redirect-rule/` Bu tek yakalama grubu tarafından yakalandıktan sonra.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-484">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="1e7e6-485">Değiştirme dizesinde, yakalanan gruplar, dolar işareti ( `$` ) ve ardından yakalamanın sıra numarası ile birlikte dizeye eklenir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-485">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="1e7e6-486">İlk yakalama grubu değeri ile elde edilir `$1` , ikincisi ile `$2` ve normal Regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-486">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="1e7e6-487">Örnek uygulamadaki yeniden yönlendirme kuralı Regex bölümünde yalnızca bir tane yakalanan grup bulunur, bu nedenle değiştirme dizesinde yalnızca bir tane eklenmiş grup vardır `$1` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-487">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="1e7e6-488">Kural uygulandığında, URL olur `/redirected/1234/5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-488">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="1e7e6-489">Güvenli bir uç noktaya URL yönlendirmesi</span><span class="sxs-lookup"><span data-stu-id="1e7e6-489">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="1e7e6-490"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*>Http ISTEKLERINI https protokolünü kullanarak aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-490">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="1e7e6-491">Durum kodu sağlanmazsa, ara yazılım varsayılan olarak *302-bulunur*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-491">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="1e7e6-492">Bağlantı noktası sağlanmazsa:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-492">If the port isn't supplied:</span></span>

* <span data-ttu-id="1e7e6-493">Ara yazılım varsayılan olarak olur `null` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-493">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="1e7e6-494">Şema `https` (https Protokolü) olarak değişir ve istemci, 443 numaralı bağlantı noktasında kaynağa erişir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-494">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="1e7e6-495">Aşağıdaki örnek, durum kodunun *301-kalıcı olarak taşınacağını* ve bağlantı noktasını 5001 olarak nasıl değiştirileceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-495">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="1e7e6-496"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*>Güvenli olmayan istekleri, bağlantı noktası 443 üzerinde GÜVENLI https Protokolü ile aynı konağa ve yola yeniden yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-496">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="1e7e6-497">Ara yazılım durum kodunu 301 olarak ayarlar ve *kalıcı olarak taşınır*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-497">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="1e7e6-498">Ek yeniden yönlendirme kuralları gereksinimi olmadan güvenli bir uç noktaya yönlendirilirken, HTTPS yeniden yönlendirme ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-498">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="1e7e6-499">Daha fazla bilgi için bkz. [https 'Yi zorla](xref:security/enforcing-ssl#require-https) konusu.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-499">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="1e7e6-500">Örnek uygulama, veya kullanımını gösterme yeteneğine sahiptir `AddRedirectToHttps` `AddRedirectToHttpsPermanent` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-500">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="1e7e6-501">Uzantı yöntemini öğesine ekleyin `RewriteOptions` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-501">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="1e7e6-502">Herhangi bir URL 'de uygulamaya güvenli olmayan bir istek oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-502">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="1e7e6-503">Otomatik olarak imzalanan sertifikanın güvenilmeyen tarayıcı güvenlik uyarısını kapatın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-503">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="1e7e6-504">Kullanarak özgün Istek `AddRedirectToHttps(301, 5001)` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-504">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="1e7e6-506">Kullanarak özgün Istek `AddRedirectToHttpsPermanent` : `http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-506">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="1e7e6-508">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="1e7e6-508">URL rewrite</span></span>

<span data-ttu-id="1e7e6-509"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*>URL 'leri yeniden yazma kuralı oluşturmak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-509">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="1e7e6-510">İlk parametre gelen URL yolundaki eşleşme için Regex içerir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-510">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="1e7e6-511">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-511">The second parameter is the replacement string.</span></span> <span data-ttu-id="1e7e6-512">Üçüncü parametresi, `skipRemainingRules: {true|false}` geçerli kural uygulanmışsa ek yeniden yazma kurallarının atlanıp atlanmayacağını gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-512">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="1e7e6-513">Özgün Istek: `/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-513">Original Request: `/rewrite-rule/1234/5678`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="1e7e6-515">İfadenin başındaki simgeyi seçtiğinizde ( `^` ), eşleşmesinin URL yolunun başlangıcında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-515">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="1e7e6-516">Önceki örnekte, yeniden yönlendirme kuralıyla, Regex başlangıcında `redirect-rule/(.*)` simgeyi seçtiğinizde ( `^` ) yoktur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-516">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="1e7e6-517">Bu nedenle, `redirect-rule/` başarılı bir eşleşme için herhangi bir karakterden önce yol açabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-517">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="1e7e6-518">Yol</span><span class="sxs-lookup"><span data-stu-id="1e7e6-518">Path</span></span>                               | <span data-ttu-id="1e7e6-519">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-519">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="1e7e6-520">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-520">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="1e7e6-521">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-521">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="1e7e6-522">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-522">Yes</span></span>   |

<span data-ttu-id="1e7e6-523">Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)` yalnızca ile başlarsa yollarla eşleşir `rewrite-rule/` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-523">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="1e7e6-524">Aşağıdaki tabloda, eşleşen farkı aklınızda.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-524">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="1e7e6-525">Yol</span><span class="sxs-lookup"><span data-stu-id="1e7e6-525">Path</span></span>                              | <span data-ttu-id="1e7e6-526">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-526">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="1e7e6-527">Yes</span><span class="sxs-lookup"><span data-stu-id="1e7e6-527">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="1e7e6-528">Hayır</span><span class="sxs-lookup"><span data-stu-id="1e7e6-528">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="1e7e6-529">Hayır</span><span class="sxs-lookup"><span data-stu-id="1e7e6-529">No</span></span>    |

<span data-ttu-id="1e7e6-530">`^rewrite-rule/`İfadenin bölümünü takip eden iki yakalama grubu vardır `(\d+)/(\d+)` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-530">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="1e7e6-531">`\d`Belirtir *bir sayıyla (sayı) eşleşir*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-531">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="1e7e6-532">Artı işareti ( `+` ) *bir veya daha fazla önceki karakterden eşleşiyor*demektir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-532">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="1e7e6-533">Bu nedenle, URL bir sayı içermeli ve ardından İleri eğik çizgi ve ardından başka bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-533">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="1e7e6-534">Bu yakalama grupları, ve olarak yeniden yazan URL 'sine `$1` eklenir `$2` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-534">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="1e7e6-535">Yeniden yazma kuralı değiştirme dizesi yakalanan grupları sorgu dizesine koyar.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-535">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="1e7e6-536">İstenen yolu, `/rewrite-rule/1234/5678` kaynağı elde etmek için yeniden yazılır `/rewritten?var1=1234&var2=5678` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-536">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="1e7e6-537">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazdığınızda korunur.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-537">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="1e7e6-538">Kaynağı almak için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-538">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="1e7e6-539">Kaynak varsa, bu, alınır ve istemciye *200-ok* durum kodu ile döndürülür.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-539">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="1e7e6-540">İstemci yeniden yönlendirmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-540">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="1e7e6-541">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamaz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-541">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="1e7e6-542">`skipRemainingRules: true`Eşleşen kuralların hesaplama maliyeti ve uygulama yanıt süresini arttığı için mümkün olan her durumda kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-542">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="1e7e6-543">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-543">For the fastest app response:</span></span>
>
> * <span data-ttu-id="1e7e6-544">En sık eşleşen kuraldan en az sıklıkta eşleşen kurala göre yeniden yazma kuralları.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-544">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="1e7e6-545">Bir eşleşme gerçekleştiğinde ve ek kural işleme gerekli olmadığında kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-545">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="1e7e6-546">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1e7e6-546">Apache mod_rewrite</span></span>

<span data-ttu-id="1e7e6-547">İle Apache mod_rewrite kuralları uygulayın <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-547">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="1e7e6-548">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-548">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e7e6-549">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için bkz. [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-549">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="1e7e6-550">, <xref:System.IO.StreamReader> *ApacheModRewrite.txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-550">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="1e7e6-551">Örnek uygulama, istekleri ' den ' e yeniden yönlendirir `/apache-mod-rules-redirect/(.\*)` `/redirected?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-551">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="1e7e6-552">Yanıt durum kodu *302-bulundu*.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-552">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="1e7e6-553">Özgün Istek: `/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-553">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![İstekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="1e7e6-555">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-555">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="1e7e6-556">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-556">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="1e7e6-557">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-557">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e7e6-558">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e7e6-558">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e7e6-559">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e7e6-559">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e7e6-560">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="1e7e6-560">HTTP_FORWARDED</span></span>
* <span data-ttu-id="1e7e6-561">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e7e6-561">HTTP_HOST</span></span>
* <span data-ttu-id="1e7e6-562">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e7e6-562">HTTP_REFERER</span></span>
* <span data-ttu-id="1e7e6-563">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-563">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e7e6-564">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e7e6-564">HTTPS</span></span>
* <span data-ttu-id="1e7e6-565">IPV6</span><span class="sxs-lookup"><span data-stu-id="1e7e6-565">IPV6</span></span>
* <span data-ttu-id="1e7e6-566">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e7e6-566">QUERY_STRING</span></span>
* <span data-ttu-id="1e7e6-567">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-567">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e7e6-568">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-568">REMOTE_PORT</span></span>
* <span data-ttu-id="1e7e6-569">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-569">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e7e6-570">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="1e7e6-570">REQUEST_METHOD</span></span>
* <span data-ttu-id="1e7e6-571">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-571">REQUEST_SCHEME</span></span>
* <span data-ttu-id="1e7e6-572">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e7e6-572">REQUEST_URI</span></span>
* <span data-ttu-id="1e7e6-573">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-573">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="1e7e6-574">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-574">SERVER_ADDR</span></span>
* <span data-ttu-id="1e7e6-575">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-575">SERVER_PORT</span></span>
* <span data-ttu-id="1e7e6-576">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="1e7e6-576">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="1e7e6-577">TIME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-577">TIME</span></span>
* <span data-ttu-id="1e7e6-578">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="1e7e6-578">TIME_DAY</span></span>
* <span data-ttu-id="1e7e6-579">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-579">TIME_HOUR</span></span>
* <span data-ttu-id="1e7e6-580">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="1e7e6-580">TIME_MIN</span></span>
* <span data-ttu-id="1e7e6-581">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="1e7e6-581">TIME_MON</span></span>
* <span data-ttu-id="1e7e6-582">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="1e7e6-582">TIME_SEC</span></span>
* <span data-ttu-id="1e7e6-583">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="1e7e6-583">TIME_WDAY</span></span>
* <span data-ttu-id="1e7e6-584">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-584">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="1e7e6-585">IIS URL yeniden yazma modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="1e7e6-585">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="1e7e6-586">IIS URL yeniden yazma modülü için geçerli olan kural kümesini kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-586">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="1e7e6-587">Kurallar dosyasının uygulamayla birlikte dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-587">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="1e7e6-588">Windows Server IIS üzerinde çalışırken uygulamanın *web.config* dosyasını kullanmak için ara yazılımı yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-588">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="1e7e6-589">IIS ile, IIS yeniden yazma modülüyle çakışmalardan kaçınmak için bu kuralların uygulamanın *web.config* dosyası dışında depolanması gerekir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-589">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="1e7e6-590">Daha fazla bilgi ve IIS URL yeniden yazma modülü kurallarının örnekleri için bkz. [Using URL yeniden yazma modülü 2,0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) ve [URL yeniden yazma modülü yapılandırma başvurusu](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-590">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="1e7e6-591">, <xref:System.IO.StreamReader> *IISUrlRewrite.xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-591">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="1e7e6-592">Örnek uygulama, ' den ' e olan istekleri yeniden yazar `/iis-rules-rewrite/(.*)` `/rewritten?id=$1` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-592">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="1e7e6-593">Yanıt, istemciye *200-ok* durum kodu ile gönderilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-593">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="1e7e6-594">Özgün Istek: `/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-594">Original Request: `/iis-rules-rewrite/1234`</span></span>

![İsteği ve yanıtı izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="1e7e6-596">Uygulamanızı istenmeyen yollarla etkileyebilecek sunucu düzeyi kurallara sahip etkin bir IIS yeniden yazma modülünüzün olması halinde, bir uygulama için IIS yeniden yazma modülünü devre dışı bırakabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-596">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="1e7e6-597">Daha fazla bilgi için bkz. [IIS modüllerini devre dışı bırakma](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span><span class="sxs-lookup"><span data-stu-id="1e7e6-597">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="1e7e6-598">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="1e7e6-598">Unsupported features</span></span>

<span data-ttu-id="1e7e6-599">ASP.NET Core 2. x ile yayınlanan ara yazılım, aşağıdaki IIS URL yeniden yazma modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-599">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="1e7e6-600">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="1e7e6-600">Outbound Rules</span></span>
* <span data-ttu-id="1e7e6-601">Özel sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="1e7e6-601">Custom Server Variables</span></span>
* <span data-ttu-id="1e7e6-602">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="1e7e6-602">Wildcards</span></span>
* <span data-ttu-id="1e7e6-603">LogRewrittenUrl 'Si</span><span class="sxs-lookup"><span data-stu-id="1e7e6-603">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="1e7e6-604">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="1e7e6-604">Supported server variables</span></span>

<span data-ttu-id="1e7e6-605">Ara yazılım aşağıdaki IIS URL yeniden yazma modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-605">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="1e7e6-606">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="1e7e6-606">CONTENT_LENGTH</span></span>
* <span data-ttu-id="1e7e6-607">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="1e7e6-607">CONTENT_TYPE</span></span>
* <span data-ttu-id="1e7e6-608">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-608">HTTP_ACCEPT</span></span>
* <span data-ttu-id="1e7e6-609">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="1e7e6-609">HTTP_CONNECTION</span></span>
* <span data-ttu-id="1e7e6-610">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="1e7e6-610">HTTP_COOKIE</span></span>
* <span data-ttu-id="1e7e6-611">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="1e7e6-611">HTTP_HOST</span></span>
* <span data-ttu-id="1e7e6-612">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="1e7e6-612">HTTP_REFERER</span></span>
* <span data-ttu-id="1e7e6-613">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="1e7e6-613">HTTP_URL</span></span>
* <span data-ttu-id="1e7e6-614">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-614">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="1e7e6-615">HTTPS</span><span class="sxs-lookup"><span data-stu-id="1e7e6-615">HTTPS</span></span>
* <span data-ttu-id="1e7e6-616">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-616">LOCAL_ADDR</span></span>
* <span data-ttu-id="1e7e6-617">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="1e7e6-617">QUERY_STRING</span></span>
* <span data-ttu-id="1e7e6-618">REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="1e7e6-618">REMOTE_ADDR</span></span>
* <span data-ttu-id="1e7e6-619">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="1e7e6-619">REMOTE_PORT</span></span>
* <span data-ttu-id="1e7e6-620">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="1e7e6-620">REQUEST_FILENAME</span></span>
* <span data-ttu-id="1e7e6-621">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="1e7e6-621">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="1e7e6-622">Ayrıca bir ile elde edebilirsiniz <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-622">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="1e7e6-623">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-623">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="1e7e6-624">Yeniden yazma kuralları dosyalarınızın sağladığınız yoldaki sunucuya dağıtıldığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-624">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="1e7e6-625">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="1e7e6-625">Method-based rule</span></span>

<span data-ttu-id="1e7e6-626"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Bir yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-626">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="1e7e6-627">`Add` , <xref:Microsoft.AspNetCore.Rewrite.RewriteContext> metodunda kullanım için kullanılabilir hale getiren öğesini gösterir <xref:Microsoft.AspNetCore.Http.HttpContext> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-627">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="1e7e6-628">[Rewritecontext. Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) , ek ardışık düzen işlemenin nasıl işlendiğini belirler.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-628">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="1e7e6-629">Değeri <xref:Microsoft.AspNetCore.Rewrite.RuleResult> Aşağıdaki tabloda açıklanan alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-629">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| <span data-ttu-id="1e7e6-630">Bağlam sonucunu yeniden yaz</span><span class="sxs-lookup"><span data-stu-id="1e7e6-630">Rewrite context result</span></span>               | <span data-ttu-id="1e7e6-631">Eylem</span><span class="sxs-lookup"><span data-stu-id="1e7e6-631">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="1e7e6-632">`RuleResult.ContinueRules` varsayılanını</span><span class="sxs-lookup"><span data-stu-id="1e7e6-632">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="1e7e6-633">Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-633">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="1e7e6-634">Kuralları uygulamayı durdurun ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-634">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="1e7e6-635">Kuralları uygulamayı durdurun ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-635">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="1e7e6-636">Örnek uygulama, *. xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntemi gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-636">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="1e7e6-637">İçin bir istek yapılırsa `/file.xml` , istek öğesine yeniden yönlendirilir `/xmlfiles/file.xml` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-637">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="1e7e6-638">Durum kodu *301*olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-638">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="1e7e6-639">Tarayıcı */xmlfiles/file.xml*için yeni bir istek yaptığında, statik dosya ara yazılımı dosyayı *Wwwroot/xmlfiles* klasöründen istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-639">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="1e7e6-640">Yeniden yönlendirme için, yanıtın durum kodunu açık olarak ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-640">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="1e7e6-641">Aksi takdirde, *200-ok* durum kodu döndürülür ve yeniden yönlendirme istemcide gerçekleşmez.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-641">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="1e7e6-642">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-642">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="1e7e6-643">Bu yaklaşım ayrıca istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-643">This approach can also rewrite requests.</span></span> <span data-ttu-id="1e7e6-644">Örnek uygulama, herhangi bir metin dosyası isteğinin yolunu *Wwwroot* klasöründen *file.txt* metin dosyasına sunacak şekilde yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-644">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="1e7e6-645">Statik dosya ara yazılımı, güncelleştirilmiş istek yoluna göre dosyayı sunar:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-645">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="1e7e6-646">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="1e7e6-646">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="1e7e6-647">Irule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="1e7e6-647">IRule-based rule</span></span>

<span data-ttu-id="1e7e6-648"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*>Arabirimini uygulayan bir sınıfta kural mantığını kullanmak için kullanın <xref:Microsoft.AspNetCore.Rewrite.IRule> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-648">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="1e7e6-649">`IRule` Yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-649">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="1e7e6-650">Uygulama sınıfınız, yöntemi için parametreleri geçirebilmeniz için bir Oluşturucu içerebilir <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-650">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="1e7e6-651">Ve için örnek uygulamadaki parametrelerin değerleri, `extension` `newPath` çeşitli koşullara uyacak şekilde denetlenir.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-651">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="1e7e6-652">`extension`Bir değer içermeli ve değer *. png*, *. jpg*veya *. gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-652">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="1e7e6-653">`newPath`Geçerli değilse, bir oluşturulur <xref:System.ArgumentException> .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-653">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="1e7e6-654">*image.png*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/png-images/image.png` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-654">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="1e7e6-655">*image.jpg*için bir istek yapılırsa, istek öğesine yeniden yönlendirilir `/jpg-images/image.jpg` .</span><span class="sxs-lookup"><span data-stu-id="1e7e6-655">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="1e7e6-656">Durum kodu 301 olarak ayarlanır ve *kalıcı olarak taşınır*ve `context.Result` kuralları işlemeyi durdur ve yanıtı gönder olarak ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="1e7e6-656">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="1e7e6-657">Özgün Istek: `/image.png`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-657">Original Request: `/image.png`</span></span>

![image.png için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="1e7e6-659">Özgün Istek: `/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-659">Original Request: `/image.jpg`</span></span>

![image.jpg için istekleri ve yanıtları izleyen Geliştirici Araçları tarayıcı penceresi](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="1e7e6-661">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="1e7e6-661">Regex examples</span></span>

| <span data-ttu-id="1e7e6-662">Hedef</span><span class="sxs-lookup"><span data-stu-id="1e7e6-662">Goal</span></span> | <span data-ttu-id="1e7e6-663">Regex dize &</span><span class="sxs-lookup"><span data-stu-id="1e7e6-663">Regex String &</span></span><br><span data-ttu-id="1e7e6-664">Match örneği</span><span class="sxs-lookup"><span data-stu-id="1e7e6-664">Match Example</span></span> | <span data-ttu-id="1e7e6-665">Değiştirme dizesi &</span><span class="sxs-lookup"><span data-stu-id="1e7e6-665">Replacement String &</span></span><br><span data-ttu-id="1e7e6-666">Çıkış örneği</span><span class="sxs-lookup"><span data-stu-id="1e7e6-666">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="1e7e6-667">Yolu QueryString 'e yeniden yazın</span><span class="sxs-lookup"><span data-stu-id="1e7e6-667">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="1e7e6-668">Eğik çizgiyi çıkar</span><span class="sxs-lookup"><span data-stu-id="1e7e6-668">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="1e7e6-669">Sondaki eğik çizgiyi zorla</span><span class="sxs-lookup"><span data-stu-id="1e7e6-669">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="1e7e6-670">Belirli istekleri yeniden yazmayı önleyin</span><span class="sxs-lookup"><span data-stu-id="1e7e6-670">Avoid rewriting specific requests</span></span> | <span data-ttu-id="1e7e6-671">`^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-671">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="1e7e6-672">Yes `/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-672">Yes: `/resource.htm`</span></span><br><span data-ttu-id="1e7e6-673">Eşleşen `/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="1e7e6-673">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="1e7e6-674">URL segmentlerini yeniden Düzenle</span><span class="sxs-lookup"><span data-stu-id="1e7e6-674">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="1e7e6-675">URL segmentini değiştirme</span><span class="sxs-lookup"><span data-stu-id="1e7e6-675">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="1e7e6-676">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="1e7e6-676">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="1e7e6-677">.NET içinde normal ifadeler</span><span class="sxs-lookup"><span data-stu-id="1e7e6-677">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="1e7e6-678">Normal ifade dili-hızlı başvuru</span><span class="sxs-lookup"><span data-stu-id="1e7e6-678">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="1e7e6-679">Apache mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="1e7e6-679">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="1e7e6-680">URL yeniden yazma modülünü kullanma 2,0 (IIS için)</span><span class="sxs-lookup"><span data-stu-id="1e7e6-680">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="1e7e6-681">URL yeniden yazma modülü yapılandırma başvurusu</span><span class="sxs-lookup"><span data-stu-id="1e7e6-681">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="1e7e6-682">IIS URL yeniden yazma modülü Forumu</span><span class="sxs-lookup"><span data-stu-id="1e7e6-682">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="1e7e6-683">Basit URL yapısını saklama</span><span class="sxs-lookup"><span data-stu-id="1e7e6-683">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="1e7e6-684">10 URL yeniden yazma Ipuçları ve püf noktaları</span><span class="sxs-lookup"><span data-stu-id="1e7e6-684">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="1e7e6-685">Eğik çizgi veya eğik çizgi</span><span class="sxs-lookup"><span data-stu-id="1e7e6-685">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
