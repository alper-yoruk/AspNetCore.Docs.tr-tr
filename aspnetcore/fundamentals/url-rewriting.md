---
title: URL ASP.NET Core'da Middleware'i Yeniden Yazma
author: rick-anderson
description: ASP.NET Core uygulamalarında URL Yeniden Yazma Middleware ile URL yeniden yazma ve yeniden yönlendirme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/16/2019
uid: fundamentals/url-rewriting
ms.openlocfilehash: 7d63cf381f1d8a19ed4fb789348e36f94304ad63
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78666469"
---
# <a name="url-rewriting-middleware-in-aspnet-core"></a><span data-ttu-id="726e4-103">URL ASP.NET Core'da Middleware'i Yeniden Yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-103">URL Rewriting Middleware in ASP.NET Core</span></span>

<span data-ttu-id="726e4-104">Yazar: [Mikael Mengistu](https://github.com/mikaelm12)</span><span class="sxs-lookup"><span data-stu-id="726e4-104">By [Mikael Mengistu](https://github.com/mikaelm12)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="726e4-105">Bu belge, ASP.NET Core uygulamalarında URL Yeniden Yazma Middleware'in nasıl kullanılacağına ilişkin yönergelerle BIRLIKTE URL yeniden yazmayı tanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-105">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="726e4-106">URL yeniden yazma, istek URL'lerini önceden tanımlanmış bir veya daha fazla kurala göre değiştirme eylemidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-106">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="726e4-107">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmamalarını sağlayacak şekilde kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="726e4-107">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="726e4-108">URL yeniden yazma çeşitli senaryolarda değerlidir:</span><span class="sxs-lookup"><span data-stu-id="726e4-108">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="726e4-109">Sunucu kaynaklarını geçici veya kalıcı olarak taşıyın veya değiştirin ve bu kaynaklar için kararlı yer bulabilenleri koruyun.</span><span class="sxs-lookup"><span data-stu-id="726e4-109">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="726e4-110">İstek işlemeyi farklı uygulamalara veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="726e4-110">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="726e4-111">Gelen isteklerde URL bölümlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-111">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="726e4-112">Arama Motoru Optimizasyonu (SEO) için genel URL'leri optimize edin.</span><span class="sxs-lookup"><span data-stu-id="726e4-112">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="726e4-113">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmelerine yardımcı olmak için dost canlı URL'lerinin kullanılmasına izin ver.</span><span class="sxs-lookup"><span data-stu-id="726e4-113">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="726e4-114">Güvenli uç noktalarına güvenli olmayan istekleri yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="726e4-114">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="726e4-115">Harici bir sitenin, varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı hotlinking'i önleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-115">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="726e4-116">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-116">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="726e4-117">Mümkün olduğu durumlarda, kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-117">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="726e4-118">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="726e4-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="726e4-119">URL yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-119">URL redirect and URL rewrite</span></span>

<span data-ttu-id="726e4-120">*URL yönlendirme* ve URL yeniden *yazma* arasındaki ifade farkı ince, ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="726e4-120">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="726e4-121">ASP.NET Core URL Rewriting Middleware her ikisi için de ihtiyacı nı karşılama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="726e4-121">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="726e4-122">*URL* yönlendirmesi, istemcinin bir kaynağa istemcinin başlangıçta talep edilenden farklı bir adreste erişmetalimatı verdiği istemci tarafı çalışmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="726e4-122">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="726e4-123">Bu sunucuya bir gidiş-dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-123">This requires a round trip to the server.</span></span> <span data-ttu-id="726e4-124">İstemci ye döndürülen yönlendirme URL'si, istemci kaynak için yeni bir istekte bulununca tarayıcının adres çubuğunda görünür.</span><span class="sxs-lookup"><span data-stu-id="726e4-124">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="726e4-125">Bu duruma *yönlendirilirse,* sunucu istemcinin kaynağı yeniden `/different-resource` yönlendirmenin geçici veya kalıcı olduğunu belirten bir durum koduyla elde etmesi gerektiği yanıtını verir. `/resource` `/different-resource`</span><span class="sxs-lookup"><span data-stu-id="726e4-125">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmet bitiş noktası, sunucudaki sürüm 1'den (v1) sürüm 2'ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="726e4-131">İstekleri farklı bir URL'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yönlendirmenin kalıcı mı yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="726e4-131">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="726e4-132">*301 - Taşınmış Kalıcı* durum kodu, kaynağın yeni ve kalıcı bir URL'si olduğu ve istemciye kaynak için gelecekteki tüm isteklerin yeni URL'yi kullanması gerektiğini söylemek istediğiniz durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-132">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="726e4-133">*İstemci, 301 durum kodu aldığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="726e4-133">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="726e4-134">*302 - Bulunan* durum kodu, yeniden yönlendirmenin geçici olduğu veya genel olarak değiştirildiği durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-134">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="726e4-135">302 durum kodu, istemciye URL'yi depolamamasını ve gelecekte kullanmamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-135">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="726e4-136">Durum kodları hakkında daha fazla bilgi için [Bkz. RFC 2616: Durum Kodu Tanımları.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="726e4-136">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="726e4-137">*URL yeniden yazma,* istemcinin istediğinden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı işlemidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-137">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="726e4-138">URL'yi yeniden yazmak için sunucuya gidiş-dönüş seyahat gerekmez.</span><span class="sxs-lookup"><span data-stu-id="726e4-138">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="726e4-139">Yeniden yazılan URL istemciye döndürülür ve tarayıcının adres çubuğunda görünmez.</span><span class="sxs-lookup"><span data-stu-id="726e4-139">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="726e4-140">Yeniden `/resource` `/different-resource` *yazılırsa,* sunucu *kaynağı 'nda* `/different-resource`dahili olarak getirir ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="726e4-140">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="726e4-141">İstemci kaynağı yeniden yazılmış URL'den alabilse de, istemci, isteği nde bulunduğunda ve yanıtı aldığında kaynağın yeniden yazılmış URL'de bulunduğu konusunda bilgilendirilmemektedir.</span><span class="sxs-lookup"><span data-stu-id="726e4-141">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI hizmet bitiş noktası sunucudaki sürüm 1 'den (v1) sürüm 2 'ye (v2) değiştirildi.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="726e4-146">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="726e4-146">URL rewriting sample app</span></span>

<span data-ttu-id="726e4-147">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)ile URL Rewriting Middleware özelliklerini keşfedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="726e4-147">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="726e4-148">Uygulama yeniden yönlendirme ve yeniden yazma kuralları nı uygular ve çeşitli senaryolar için yeniden yönlendirilen veya yeniden yazılan URL'yi gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-148">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="726e4-149">URL Rewriting Middleware ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="726e4-149">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="726e4-150">Aşağıdaki yaklaşımları kullanamadığınızda URL Yeniden Yazma Middleware'i kullanın:</span><span class="sxs-lookup"><span data-stu-id="726e4-150">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="726e4-151">Windows Server'da IIS ile URL Yeniden Yazma Modülü</span><span class="sxs-lookup"><span data-stu-id="726e4-151">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="726e4-152">Apache Server'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="726e4-152">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="726e4-153">Nginx'te URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-153">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="726e4-154">Ayrıca, uygulama [HTTP.sys sunucusunda](xref:fundamentals/servers/httpsys) (eski adıyla WebListener olarak adlandırılır) barındırıldığında ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-154">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="726e4-155">IIS, Apache ve Nginx'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="726e4-155">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="726e4-156">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="726e4-156">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="726e4-157">Sunucu modüllerinin bazı özellikleri, IIS Yeniden Yazma modülünün `IsFile` kısıtlamaları `IsDirectory` ve kısıtlamaları gibi ASP.NET Core projeleri ile çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="726e4-157">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="726e4-158">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-158">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="726e4-159">Ara yazılımın performansı muhtemelen modüllerin performansıyla eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="726e4-159">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="726e4-160">Kıyaslama, hangi yaklaşımın performansı en çok düşürdüğünü veya azalan performansın ihmal edilebilir olup olmadığını kesin olarak bilmenin tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="726e4-160">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="726e4-161">Paket</span><span class="sxs-lookup"><span data-stu-id="726e4-161">Package</span></span>

<span data-ttu-id="726e4-162">URL Rewriting Middleware, ASP.NET Core uygulamalarına dolaylı olarak dahil edilen [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketi tarafından sağlanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="726e4-162">URL Rewriting Middleware is provided by the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package, which is implicitly included in ASP.NET Core apps.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="726e4-163">Uzantı ve seçenekler</span><span class="sxs-lookup"><span data-stu-id="726e4-163">Extension and options</span></span>

<span data-ttu-id="726e4-164">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriiçeren [Yeniden Yazma Seçenekleri](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir örneğini oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları nı belirleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-164">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="726e4-165">İşlenmelerini istediğiniz sırayla birden çok kural zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-165">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="726e4-166">İstek `RewriteOptions` ardışık hattına eklenerek URL Yeniden Yazma Middleware'e <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>aktarılır:</span><span class="sxs-lookup"><span data-stu-id="726e4-166">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="726e4-167">Www olmayanları www'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="726e4-167">Redirect non-www to www</span></span>

<span data-ttu-id="726e4-168">Üç seçenek, uygulamanın olmayan`www` istekleri `www`aşağıdakilere yönlendirmesine izin verir:</span><span class="sxs-lookup"><span data-stu-id="726e4-168">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="726e4-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; İstek yoksa, isteği kalıcı `www` olarak alt etki alanına`www`yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="726e4-169"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="726e4-170">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum koduyla yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-170">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="726e4-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Gelen istek yoksa, isteği `www` alt etki alanına`www`yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="726e4-171"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="726e4-172">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum koduyla yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-172">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="726e4-173">Aşırı yükleme, yanıtın durum kodunu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="726e4-173">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="726e4-174">Durum kodu ataması <xref:Microsoft.AspNetCore.Http.StatusCodes> için sınıfın alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-174">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="726e4-175">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="726e4-175">URL redirect</span></span>

<span data-ttu-id="726e4-176">İstekleri yönlendirmek için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*></span><span class="sxs-lookup"><span data-stu-id="726e4-176">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="726e4-177">İlk parametre, gelen URL'nin yolunda eşleştirme için regex'inizi içerir.</span><span class="sxs-lookup"><span data-stu-id="726e4-177">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="726e4-178">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-178">The second parameter is the replacement string.</span></span> <span data-ttu-id="726e4-179">Üçüncü parametre, varsa durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="726e4-179">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="726e4-180">Durum kodunu belirtmezseniz, durum kodu varsayılan *olarak 302 - Found*, kaynağın geçici olarak taşındığını veya değiştirilmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-180">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="726e4-181">Geliştirici araçları etkin leştirilmiş bir tarayıcıda, yol `/redirect-rule/1234/5678`ile örnek uygulama için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="726e4-181">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="726e4-182">Regex, istek yolu `redirect-rule/(.*)`üzerinde eşleşir ve yol `/redirected/1234/5678`' ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-182">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="726e4-183">Yönlendirme URL'si *302 - Found* durum koduyla istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-183">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="726e4-184">Tarayıcı, tarayıcının adres çubuğunda görünen yönlendirme URL'sinde yeni bir istekte bulunr.</span><span class="sxs-lookup"><span data-stu-id="726e4-184">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="726e4-185">Örnek uygulamadaki kural yönlendirme URL'sinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="726e4-185">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="726e4-186">İkinci istek uygulamadan *200 - Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="726e4-186">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="726e4-187">Yanıtın gövdesi yönlendirme URL'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-187">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="726e4-188">URL *yönlendirildiğinde*sunucuya gidiş-dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-188">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="726e4-189">Yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-189">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="726e4-190">Yönlendirme kuralları, yönlendirme sonrası da dahil olmak üzere uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-190">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="726e4-191">Yanlışlıkla *sonsuz yönlendirmeler*bir döngü oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="726e4-191">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="726e4-192">Orijinal İstek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="726e4-192">Original Request: `/redirect-rule/1234/5678`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="726e4-194">İfadenin parantez içinde bulunan bölümüne *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="726e4-194">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="726e4-195">İfadenin nokta`.`( ) *herhangi bir karakterle eşleşmesi*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="726e4-195">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="726e4-196">Yıldız işareti (`*`) *önceki karakter sıfır veya daha fazla kez eşleşir.*</span><span class="sxs-lookup"><span data-stu-id="726e4-196">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="726e4-197">Bu nedenle, URL'nin son iki `1234/5678`yol kesimi, `(.*)`yakalama grubu tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-197">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="726e4-198">İstek URL'sinde `redirect-rule/` sağladığınız herhangi bir değer bu tek yakalama grubu tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-198">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="726e4-199">Yedek dizede, yakalanan gruplar dolar işareti ile dize içine enjekte edilir (`$`) yakalama sıra numarası takip.</span><span class="sxs-lookup"><span data-stu-id="726e4-199">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="726e4-200">İlk yakalama grubu değeri `$1`, ikinci `$2`ile elde edilir ve regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="726e4-200">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="726e4-201">Örnek uygulamasında yönlendirme kuralı regex'te yakalanan tek bir grup var. `$1`</span><span class="sxs-lookup"><span data-stu-id="726e4-201">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="726e4-202">Kural uygulandığında, URL olur. `/redirected/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="726e4-202">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="726e4-203">URL güvenli bir bitiş noktasına yönlendirilir</span><span class="sxs-lookup"><span data-stu-id="726e4-203">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="726e4-204">HTTPS <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> protokolünü kullanarak HTTP isteklerini aynı ana bilgisayara ve yola yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-204">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="726e4-205">Durum kodu sağlanmıyorsa, ara yazılım varsayılan *olarak 302 - Bulundu.*</span><span class="sxs-lookup"><span data-stu-id="726e4-205">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="726e4-206">Bağlantı noktası sağlanmıyorsa:</span><span class="sxs-lookup"><span data-stu-id="726e4-206">If the port isn't supplied:</span></span>

* <span data-ttu-id="726e4-207">Ara yazılım varsayılan `null`olarak .</span><span class="sxs-lookup"><span data-stu-id="726e4-207">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="726e4-208">Şema (HTTPS protokolü) olarak `https` değişir ve istemci bağlantı noktası 443'teki kaynağa erişer.</span><span class="sxs-lookup"><span data-stu-id="726e4-208">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="726e4-209">Aşağıdaki örnek, durum *kodunun 301 - Moved Permanent'e* nasıl ayarlanır ve bağlantı noktasını 5001 olarak nasıl değiştireceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-209">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="726e4-210">Güvenli <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> olmayan istekleri aynı ana bilgisayara ve bağlantı noktası 443'teki güvenli HTTPS protokolüyle yola yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-210">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="726e4-211">Ara yazılım durum kodunu *301*olarak ayarlar - Kalıcı olarak taşınır.</span><span class="sxs-lookup"><span data-stu-id="726e4-211">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="726e4-212">Ek yeniden yönlendirme kuralları gerekmeden güvenli bir bitiş noktasına yönlendirirken, HTTPS Yönlendirme Middleware'i kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="726e4-212">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="726e4-213">Daha fazla bilgi için [HTTPS'yi Uygula](xref:security/enforcing-ssl#require-https) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="726e4-213">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="726e4-214">Örnek uygulama nasıl kullanılacağını veya `AddRedirectToHttps` `AddRedirectToHttpsPermanent`nasıl kullanılacağını gösterebilme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="726e4-214">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="726e4-215">Uzantı yöntemini `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="726e4-215">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="726e4-216">Herhangi bir URL'den uygulamaya güvenli olmayan bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="726e4-216">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="726e4-217">Tarayıcı güvenlik uyarısını, kendi imzaladığı sertifikanın güvenilen olmadığı konusunda görevden alın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="726e4-217">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="726e4-218">Orijinal İstek `AddRedirectToHttps(301, 5001)`kullanarak:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="726e4-218">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="726e4-220">Orijinal İstek `AddRedirectToHttpsPermanent`kullanarak:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="726e4-220">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="726e4-222">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-222">URL rewrite</span></span>

<span data-ttu-id="726e4-223">URL'leri yeniden yazmak için bir kural oluşturmak için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*></span><span class="sxs-lookup"><span data-stu-id="726e4-223">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="726e4-224">İlk parametre, gelen URL yolunda eşleştirme için regex içerir.</span><span class="sxs-lookup"><span data-stu-id="726e4-224">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="726e4-225">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-225">The second parameter is the replacement string.</span></span> <span data-ttu-id="726e4-226">Üçüncü parametre, `skipRemainingRules: {true|false}`geçerli kural uygulanırsa ek yeniden yazma kuralları atlamak olsun veya olmasın middleware gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-226">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="726e4-227">Orijinal İstek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="726e4-227">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="726e4-229">İfadenin başındaki karat (`^`) eşleştirmenin URL yolunun başında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="726e4-229">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="726e4-230">Yönlendirme kuralı ile önceki örnekte, `redirect-rule/(.*)`regex başında hiçbir`^`karat () var.</span><span class="sxs-lookup"><span data-stu-id="726e4-230">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="726e4-231">Bu nedenle, herhangi bir `redirect-rule/` karakter başarılı bir eşleşme için yolda önce olabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-231">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="726e4-232">Yol</span><span class="sxs-lookup"><span data-stu-id="726e4-232">Path</span></span>                               | <span data-ttu-id="726e4-233">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="726e4-233">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="726e4-234">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-234">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="726e4-235">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-235">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="726e4-236">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-236">Yes</span></span>   |

<span data-ttu-id="726e4-237">Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)`yalnızca ' ile `rewrite-rule/`başlarlarsa yollar eşleşir.</span><span class="sxs-lookup"><span data-stu-id="726e4-237">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="726e4-238">Aşağıdaki tabloda, eşleştirme farkı dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="726e4-238">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="726e4-239">Yol</span><span class="sxs-lookup"><span data-stu-id="726e4-239">Path</span></span>                              | <span data-ttu-id="726e4-240">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="726e4-240">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="726e4-241">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-241">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="726e4-242">Hayır</span><span class="sxs-lookup"><span data-stu-id="726e4-242">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="726e4-243">Hayır</span><span class="sxs-lookup"><span data-stu-id="726e4-243">No</span></span>    |

<span data-ttu-id="726e4-244">İfadenin `^rewrite-rule/` bir bölümünü takiben, iki `(\d+)/(\d+)`yakalama grubu vardır.</span><span class="sxs-lookup"><span data-stu-id="726e4-244">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="726e4-245">Bir `\d` basamak *(sayı) eşleşmesi*anlamına gelen.</span><span class="sxs-lookup"><span data-stu-id="726e4-245">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="726e4-246">Artı işareti`+`( ) *önceki karakterin bir veya daha fazla eşleşmesi*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="726e4-246">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="726e4-247">Bu nedenle, URL bir sayı ve ardından başka bir sayı takip eden bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-247">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="726e4-248">Bu yakalama grupları yeniden yazılan URL'ye `$2`. `$1`</span><span class="sxs-lookup"><span data-stu-id="726e4-248">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="726e4-249">Yeniden yazma kuralı değiştirme dizesi, yakalanan grupları sorgu dizesine yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-249">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="726e4-250">İstenen `/rewrite-rule/1234/5678` yol, kaynağı n ' `/rewritten?var1=1234&var2=5678`den elde etmek için yeniden yazılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-250">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="726e4-251">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazıldığında korunur.</span><span class="sxs-lookup"><span data-stu-id="726e4-251">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="726e4-252">Kaynağı elde etmek için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="726e4-252">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="726e4-253">Kaynak varsa, *200 - Tamam* durum koduyla getirilir ve istemciye döndürülür.</span><span class="sxs-lookup"><span data-stu-id="726e4-253">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="726e4-254">İstemci yönlendirilmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="726e4-254">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="726e4-255">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamamaktadır.</span><span class="sxs-lookup"><span data-stu-id="726e4-255">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="726e4-256">Eşleşen `skipRemainingRules: true` kurallar hesaplama olarak pahalı olduğundan ve uygulama yanıt süresini artırdığından, mümkün olduğunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-256">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="726e4-257">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="726e4-257">For the fastest app response:</span></span>
>
> * <span data-ttu-id="726e4-258">En sık eşleşen kuraldan en sık eşleşen kurala yeniden yazma kurallarını sırala.</span><span class="sxs-lookup"><span data-stu-id="726e4-258">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="726e4-259">Eşleşme oluştuğunda ve ek kural işleme gerektirildiğinde kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-259">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="726e4-260">Apaçi mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="726e4-260">Apache mod_rewrite</span></span>

<span data-ttu-id="726e4-261">Apache mod_rewrite kurallarını <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="726e4-261">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="726e4-262">Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-262">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="726e4-263">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için, [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)bakın.</span><span class="sxs-lookup"><span data-stu-id="726e4-263">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="726e4-264"><xref:System.IO.StreamReader> A, *ApacheModRewrite.txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="726e4-264">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="726e4-265">Örnek uygulama istekleri ' `/apache-mod-rules-redirect/(.\*)` den `/redirected?id=$1`' e yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-265">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="726e4-266">Yanıt durum kodu *302 - Bulundu.*</span><span class="sxs-lookup"><span data-stu-id="726e4-266">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/3.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="726e4-267">Orijinal İstek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="726e4-267">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="726e4-269">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="726e4-269">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="726e4-270">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="726e4-270">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="726e4-271">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="726e4-271">HTTP_ACCEPT</span></span>
* <span data-ttu-id="726e4-272">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="726e4-272">HTTP_CONNECTION</span></span>
* <span data-ttu-id="726e4-273">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="726e4-273">HTTP_COOKIE</span></span>
* <span data-ttu-id="726e4-274">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="726e4-274">HTTP_FORWARDED</span></span>
* <span data-ttu-id="726e4-275">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="726e4-275">HTTP_HOST</span></span>
* <span data-ttu-id="726e4-276">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="726e4-276">HTTP_REFERER</span></span>
* <span data-ttu-id="726e4-277">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="726e4-277">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="726e4-278">HTTPS</span><span class="sxs-lookup"><span data-stu-id="726e4-278">HTTPS</span></span>
* <span data-ttu-id="726e4-279">IPV6</span><span class="sxs-lookup"><span data-stu-id="726e4-279">IPV6</span></span>
* <span data-ttu-id="726e4-280">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="726e4-280">QUERY_STRING</span></span>
* <span data-ttu-id="726e4-281">Remote_addr</span><span class="sxs-lookup"><span data-stu-id="726e4-281">REMOTE_ADDR</span></span>
* <span data-ttu-id="726e4-282">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="726e4-282">REMOTE_PORT</span></span>
* <span data-ttu-id="726e4-283">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="726e4-283">REQUEST_FILENAME</span></span>
* <span data-ttu-id="726e4-284">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="726e4-284">REQUEST_METHOD</span></span>
* <span data-ttu-id="726e4-285">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="726e4-285">REQUEST_SCHEME</span></span>
* <span data-ttu-id="726e4-286">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="726e4-286">REQUEST_URI</span></span>
* <span data-ttu-id="726e4-287">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="726e4-287">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="726e4-288">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="726e4-288">SERVER_ADDR</span></span>
* <span data-ttu-id="726e4-289">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="726e4-289">SERVER_PORT</span></span>
* <span data-ttu-id="726e4-290">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="726e4-290">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="726e4-291">TIME</span><span class="sxs-lookup"><span data-stu-id="726e4-291">TIME</span></span>
* <span data-ttu-id="726e4-292">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="726e4-292">TIME_DAY</span></span>
* <span data-ttu-id="726e4-293">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="726e4-293">TIME_HOUR</span></span>
* <span data-ttu-id="726e4-294">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="726e4-294">TIME_MIN</span></span>
* <span data-ttu-id="726e4-295">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="726e4-295">TIME_MON</span></span>
* <span data-ttu-id="726e4-296">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="726e4-296">TIME_SEC</span></span>
* <span data-ttu-id="726e4-297">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="726e4-297">TIME_WDAY</span></span>
* <span data-ttu-id="726e4-298">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="726e4-298">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="726e4-299">IIS URL Yeniden Yazma Modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="726e4-299">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="726e4-300">IIS URL Yeniden Yazma Modülü için geçerli olan aynı <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>kural kümesini kullanmak için .</span><span class="sxs-lookup"><span data-stu-id="726e4-300">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="726e4-301">Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-301">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="726e4-302">Windows Server IIS'de çalışırken ara yazılımı uygulamanın *web.config* dosyasını kullanmaya yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-302">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="726e4-303">IIS ile bu kurallar, IIS Yeniden Yazma modülüyle çakışmaları önlemek için uygulamanın *web.config* dosyasının dışında saklanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="726e4-303">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="726e4-304">IIS URL Yeniden Yazma Modülü kuralları hakkında daha fazla bilgi [ve](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) örnekler için [bkz.](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span><span class="sxs-lookup"><span data-stu-id="726e4-304">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="726e4-305"><xref:System.IO.StreamReader> A, *IISUrlRewrite.xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="726e4-305">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="726e4-306">Örnek uygulama isteklerini `/iis-rules-rewrite/(.*)` 'den ' e `/rewritten?id=$1`yeniden yazar</span><span class="sxs-lookup"><span data-stu-id="726e4-306">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="726e4-307">Yanıt istemciye *200 - Tamam* durum koduyla gönderilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-307">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/3.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="726e4-308">Orijinal İstek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="726e4-308">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="726e4-310">Uygulamanızı istenmeyen şekillerde etkileyecek şekilde yapılandırılan sunucu düzeyinde kurallara sahip etkin bir IIS Yeniden Yazma Modülünüz varsa, bir uygulama için IIS Yeniden Yazma Modül'ünden devre dışı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="726e4-310">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="726e4-311">Daha fazla bilgi için Bkz. [IIS modüllerini devre dışı bırakma.](xref:host-and-deploy/iis/modules#disabling-iis-modules)</span><span class="sxs-lookup"><span data-stu-id="726e4-311">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="726e4-312">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="726e4-312">Unsupported features</span></span>

<span data-ttu-id="726e4-313">Core 2.x ile ASP.NET yayımlanan ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="726e4-313">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="726e4-314">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="726e4-314">Outbound Rules</span></span>
* <span data-ttu-id="726e4-315">Özel Sunucu Değişkenleri</span><span class="sxs-lookup"><span data-stu-id="726e4-315">Custom Server Variables</span></span>
* <span data-ttu-id="726e4-316">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="726e4-316">Wildcards</span></span>
* <span data-ttu-id="726e4-317">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="726e4-317">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="726e4-318">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="726e4-318">Supported server variables</span></span>

<span data-ttu-id="726e4-319">Ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="726e4-319">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="726e4-320">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="726e4-320">CONTENT_LENGTH</span></span>
* <span data-ttu-id="726e4-321">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="726e4-321">CONTENT_TYPE</span></span>
* <span data-ttu-id="726e4-322">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="726e4-322">HTTP_ACCEPT</span></span>
* <span data-ttu-id="726e4-323">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="726e4-323">HTTP_CONNECTION</span></span>
* <span data-ttu-id="726e4-324">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="726e4-324">HTTP_COOKIE</span></span>
* <span data-ttu-id="726e4-325">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="726e4-325">HTTP_HOST</span></span>
* <span data-ttu-id="726e4-326">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="726e4-326">HTTP_REFERER</span></span>
* <span data-ttu-id="726e4-327">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="726e4-327">HTTP_URL</span></span>
* <span data-ttu-id="726e4-328">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="726e4-328">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="726e4-329">HTTPS</span><span class="sxs-lookup"><span data-stu-id="726e4-329">HTTPS</span></span>
* <span data-ttu-id="726e4-330">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="726e4-330">LOCAL_ADDR</span></span>
* <span data-ttu-id="726e4-331">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="726e4-331">QUERY_STRING</span></span>
* <span data-ttu-id="726e4-332">Remote_addr</span><span class="sxs-lookup"><span data-stu-id="726e4-332">REMOTE_ADDR</span></span>
* <span data-ttu-id="726e4-333">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="726e4-333">REMOTE_PORT</span></span>
* <span data-ttu-id="726e4-334">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="726e4-334">REQUEST_FILENAME</span></span>
* <span data-ttu-id="726e4-335">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="726e4-335">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="726e4-336">Ayrıca bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>üzerinden elde edebilirsiniz .</span><span class="sxs-lookup"><span data-stu-id="726e4-336">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="726e4-337">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-337">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="726e4-338">Yeniden yazma kuralları dosyalarınızın sağladığınız yolda sunucuya dağıtıldığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-338">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="726e4-339">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="726e4-339">Method-based rule</span></span>

<span data-ttu-id="726e4-340">Bir <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-340">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="726e4-341">`Add`<xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, yönteminizde kullanıma hazır <xref:Microsoft.AspNetCore.Http.HttpContext> hale getirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-341">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="726e4-342">[Yeniden Yazma Bağlamı.Sonuç,](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) ek boru hattı işlemenin nasıl işleneceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="726e4-342">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="726e4-343">Değeri aşağıdaki tabloda <xref:Microsoft.AspNetCore.Rewrite.RuleResult> açıklanan alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-343">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="726e4-344">Eylem</span><span class="sxs-lookup"><span data-stu-id="726e4-344">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="726e4-345">`RuleResult.ContinueRules`(varsayılan)</span><span class="sxs-lookup"><span data-stu-id="726e4-345">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="726e4-346">Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="726e4-346">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="726e4-347">Kuralları uygulamayı bırakın ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="726e4-347">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="726e4-348">Kuralları uygulamayı bırakın ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="726e4-348">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="726e4-349">Örnek *uygulama.xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntem gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-349">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="726e4-350">Bir `/file.xml`istek için yapılırsa, istek `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="726e4-350">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="726e4-351">Durum kodu *301*olarak ayarlanır - Kalıcı olarak taşınır.</span><span class="sxs-lookup"><span data-stu-id="726e4-351">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="726e4-352">Tarayıcı */xmlfiles/file.xml*için yeni bir istekte bulunursa, Statik Dosya Middleware *wwwroot/xmlfiles* klasöründen dosyayı istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="726e4-352">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="726e4-353">Yeniden yönlendirme için yanıtın durum kodunu açıkça ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-353">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="726e4-354">Aksi takdirde, *200 - Tamam* durum kodu döndürülür ve yönlendirme istemcide oluşmaz.</span><span class="sxs-lookup"><span data-stu-id="726e4-354">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="726e4-355">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="726e4-355">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="726e4-356">Bu yaklaşım istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-356">This approach can also rewrite requests.</span></span> <span data-ttu-id="726e4-357">Örnek *uygulama, wwwroot* klasöründen *file.txt* metin dosyasına hizmet vermek için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-357">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="726e4-358">Statik Dosya Middleware güncelleştirilmiş istek yolunu temel alarak dosyaya hizmet vermektedir:</span><span class="sxs-lookup"><span data-stu-id="726e4-358">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="726e4-359">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="726e4-359">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="726e4-360">IRule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="726e4-360">IRule-based rule</span></span>

<span data-ttu-id="726e4-361">Arabirimi uygulayan bir sınıfta kural mantığını kullanmak için <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> <xref:Microsoft.AspNetCore.Rewrite.IRule> kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-361">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="726e4-362">`IRule`yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="726e4-362">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="726e4-363">Uygulama sınıfınız, <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> yöntem için parametreleri geçirmenize olanak tanıyan bir oluşturucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-363">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="726e4-364">Örnek uygulamadaki parametrelerin değerleri için `extension` ve `newPath` çeşitli koşulları karşılamak için kontrol edilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-364">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="726e4-365">Bir `extension` değer içermelidir ve değeri *.png*, *.jpg*veya *.gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="726e4-365">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="726e4-366">Geçerli `newPath` değilse, bir <xref:System.ArgumentException> atılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-366">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="726e4-367">*image.png*için bir istek yapılırsa, `/png-images/image.png`istek .</span><span class="sxs-lookup"><span data-stu-id="726e4-367">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="726e4-368">*image.jpg*için bir istek yapılırsa, `/jpg-images/image.jpg`istek .</span><span class="sxs-lookup"><span data-stu-id="726e4-368">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="726e4-369">Durum kodu *301*olarak ayarlanır - Kalıcı `context.Result` olarak taşınır ve işleme kurallarını durdurmak ve yanıtı göndermek için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-369">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/3.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="726e4-370">Orijinal İstek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="726e4-370">Original Request: `/image.png`</span></span>

![Geliştirici Araçları ile tarayıcı penceresi image.png için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="726e4-372">Orijinal İstek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="726e4-372">Original Request: `/image.jpg`</span></span>

![Geliştirici Araçları ile tarayıcı penceresi image.jpg için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="726e4-374">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="726e4-374">Regex examples</span></span>

| <span data-ttu-id="726e4-375">Hedef</span><span class="sxs-lookup"><span data-stu-id="726e4-375">Goal</span></span> | <span data-ttu-id="726e4-376">Regex String &</span><span class="sxs-lookup"><span data-stu-id="726e4-376">Regex String &</span></span><br><span data-ttu-id="726e4-377">Eşle' nin Örneği</span><span class="sxs-lookup"><span data-stu-id="726e4-377">Match Example</span></span> | <span data-ttu-id="726e4-378">Yedek Dize &</span><span class="sxs-lookup"><span data-stu-id="726e4-378">Replacement String &</span></span><br><span data-ttu-id="726e4-379">Çıktı Örneği</span><span class="sxs-lookup"><span data-stu-id="726e4-379">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="726e4-380">Yolu querystring'e yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-380">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="726e4-381">Şerit izleme eğik çizgi</span><span class="sxs-lookup"><span data-stu-id="726e4-381">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="726e4-382">Sondaki eğik çizgiyi zorla</span><span class="sxs-lookup"><span data-stu-id="726e4-382">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="726e4-383">Belirli istekleri yeniden yazmaktan kaçının</span><span class="sxs-lookup"><span data-stu-id="726e4-383">Avoid rewriting specific requests</span></span> | <span data-ttu-id="726e4-384">`^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="726e4-384">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="726e4-385">Evet:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="726e4-385">Yes: `/resource.htm`</span></span><br><span data-ttu-id="726e4-386">№:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="726e4-386">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="726e4-387">URL segmentlerini yeniden düzenleme</span><span class="sxs-lookup"><span data-stu-id="726e4-387">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="726e4-388">URL kesimini değiştirme</span><span class="sxs-lookup"><span data-stu-id="726e4-388">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="726e4-389">Bu belge, ASP.NET Core uygulamalarında URL Yeniden Yazma Middleware'in nasıl kullanılacağına ilişkin yönergelerle BIRLIKTE URL yeniden yazmayı tanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-389">This document introduces URL rewriting with instructions on how to use URL Rewriting Middleware in ASP.NET Core apps.</span></span>

<span data-ttu-id="726e4-390">URL yeniden yazma, istek URL'lerini önceden tanımlanmış bir veya daha fazla kurala göre değiştirme eylemidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-390">URL rewriting is the act of modifying request URLs based on one or more predefined rules.</span></span> <span data-ttu-id="726e4-391">URL yeniden yazma, konumların ve adreslerin sıkı bir şekilde bağlanmamalarını sağlayacak şekilde kaynak konumları ve adresleri arasında bir soyutlama oluşturur.</span><span class="sxs-lookup"><span data-stu-id="726e4-391">URL rewriting creates an abstraction between resource locations and their addresses so that the locations and addresses aren't tightly linked.</span></span> <span data-ttu-id="726e4-392">URL yeniden yazma çeşitli senaryolarda değerlidir:</span><span class="sxs-lookup"><span data-stu-id="726e4-392">URL rewriting is valuable in several scenarios to:</span></span>

* <span data-ttu-id="726e4-393">Sunucu kaynaklarını geçici veya kalıcı olarak taşıyın veya değiştirin ve bu kaynaklar için kararlı yer bulabilenleri koruyun.</span><span class="sxs-lookup"><span data-stu-id="726e4-393">Move or replace server resources temporarily or permanently and maintain stable locators for those resources.</span></span>
* <span data-ttu-id="726e4-394">İstek işlemeyi farklı uygulamalara veya bir uygulamanın alanlarında bölme.</span><span class="sxs-lookup"><span data-stu-id="726e4-394">Split request processing across different apps or across areas of one app.</span></span>
* <span data-ttu-id="726e4-395">Gelen isteklerde URL bölümlerini kaldırın, ekleyin veya yeniden düzenleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-395">Remove, add, or reorganize URL segments on incoming requests.</span></span>
* <span data-ttu-id="726e4-396">Arama Motoru Optimizasyonu (SEO) için genel URL'leri optimize edin.</span><span class="sxs-lookup"><span data-stu-id="726e4-396">Optimize public URLs for Search Engine Optimization (SEO).</span></span>
* <span data-ttu-id="726e4-397">Ziyaretçilerin bir kaynak isteyerek döndürülen içeriği tahmin etmelerine yardımcı olmak için dost canlı URL'lerinin kullanılmasına izin ver.</span><span class="sxs-lookup"><span data-stu-id="726e4-397">Permit the use of friendly public URLs to help visitors predict the content returned by requesting a resource.</span></span>
* <span data-ttu-id="726e4-398">Güvenli uç noktalarına güvenli olmayan istekleri yeniden yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="726e4-398">Redirect insecure requests to secure endpoints.</span></span>
* <span data-ttu-id="726e4-399">Harici bir sitenin, varlığı kendi içeriğine bağlayarak başka bir sitede barındırılan statik bir varlık kullandığı hotlinking'i önleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-399">Prevent hotlinking, where an external site uses a hosted static asset on another site by linking the asset into its own content.</span></span>

> [!NOTE]
> <span data-ttu-id="726e4-400">URL yeniden yazma, bir uygulamanın performansını azaltabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-400">URL rewriting can reduce the performance of an app.</span></span> <span data-ttu-id="726e4-401">Mümkün olduğu durumlarda, kuralların sayısını ve karmaşıklığını sınırlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-401">Where feasible, limit the number and complexity of rules.</span></span>

<span data-ttu-id="726e4-402">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="726e4-402">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="url-redirect-and-url-rewrite"></a><span data-ttu-id="726e4-403">URL yönlendirme ve URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-403">URL redirect and URL rewrite</span></span>

<span data-ttu-id="726e4-404">*URL yönlendirme* ve URL yeniden *yazma* arasındaki ifade farkı ince, ancak istemcilere kaynak sağlamak için önemli etkileri vardır.</span><span class="sxs-lookup"><span data-stu-id="726e4-404">The difference in wording between *URL redirect* and *URL rewrite* is subtle but has important implications for providing resources to clients.</span></span> <span data-ttu-id="726e4-405">ASP.NET Core URL Rewriting Middleware her ikisi için de ihtiyacı nı karşılama yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="726e4-405">ASP.NET Core's URL Rewriting Middleware is capable of meeting the need for both.</span></span>

<span data-ttu-id="726e4-406">*URL* yönlendirmesi, istemcinin bir kaynağa istemcinin başlangıçta talep edilenden farklı bir adreste erişmetalimatı verdiği istemci tarafı çalışmasını içerir.</span><span class="sxs-lookup"><span data-stu-id="726e4-406">A *URL redirect* involves a client-side operation, where the client is instructed to access a resource at a different address than the client originally requested.</span></span> <span data-ttu-id="726e4-407">Bu sunucuya bir gidiş-dönüş gerektirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-407">This requires a round trip to the server.</span></span> <span data-ttu-id="726e4-408">İstemci ye döndürülen yönlendirme URL'si, istemci kaynak için yeni bir istekte bulununca tarayıcının adres çubuğunda görünür.</span><span class="sxs-lookup"><span data-stu-id="726e4-408">The redirect URL returned to the client appears in the browser's address bar when the client makes a new request for the resource.</span></span>

<span data-ttu-id="726e4-409">Bu duruma *yönlendirilirse,* sunucu istemcinin kaynağı yeniden `/different-resource` yönlendirmenin geçici veya kalıcı olduğunu belirten bir durum koduyla elde etmesi gerektiği yanıtını verir. `/resource` `/different-resource`</span><span class="sxs-lookup"><span data-stu-id="726e4-409">If `/resource` is *redirected* to `/different-resource`, the server responds that the client should obtain the resource at `/different-resource` with a status code indicating that the redirect is either temporary or permanent.</span></span>

![Bir WebAPI hizmet bitiş noktası, sunucudaki sürüm 1'den (v1) sürüm 2'ye (v2) geçici olarak değiştirildi.](url-rewriting/_static/url_redirect.png)

<span data-ttu-id="726e4-415">İstekleri farklı bir URL'ye yönlendirirken, yanıtla birlikte durum kodunu belirterek yönlendirmenin kalıcı mı yoksa geçici mi olduğunu belirtin:</span><span class="sxs-lookup"><span data-stu-id="726e4-415">When redirecting requests to a different URL, indicate whether the redirect is permanent or temporary by specifying the status code with the response:</span></span>

* <span data-ttu-id="726e4-416">*301 - Taşınmış Kalıcı* durum kodu, kaynağın yeni ve kalıcı bir URL'si olduğu ve istemciye kaynak için gelecekteki tüm isteklerin yeni URL'yi kullanması gerektiğini söylemek istediğiniz durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-416">The *301 - Moved Permanently* status code is used where the resource has a new, permanent URL and you wish to instruct the client that all future requests for the resource should use the new URL.</span></span> <span data-ttu-id="726e4-417">*İstemci, 301 durum kodu aldığında yanıtı önbelleğe alabilir ve yeniden kullanabilir.*</span><span class="sxs-lookup"><span data-stu-id="726e4-417">*The client may cache and reuse the response when a 301 status code is received.*</span></span>

* <span data-ttu-id="726e4-418">*302 - Bulunan* durum kodu, yeniden yönlendirmenin geçici olduğu veya genel olarak değiştirildiği durumlarda kullanılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-418">The *302 - Found* status code is used where the redirection is temporary or generally subject to change.</span></span> <span data-ttu-id="726e4-419">302 durum kodu, istemciye URL'yi depolamamasını ve gelecekte kullanmamasını gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-419">The 302 status code indicates to the client not to store the URL and use it in the future.</span></span>

<span data-ttu-id="726e4-420">Durum kodları hakkında daha fazla bilgi için [Bkz. RFC 2616: Durum Kodu Tanımları.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)</span><span class="sxs-lookup"><span data-stu-id="726e4-420">For more information on status codes, see [RFC 2616: Status Code Definitions](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).</span></span>

<span data-ttu-id="726e4-421">*URL yeniden yazma,* istemcinin istediğinden farklı bir kaynak adresinden kaynak sağlayan sunucu tarafı işlemidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-421">A *URL rewrite* is a server-side operation that provides a resource from a different resource address than the client requested.</span></span> <span data-ttu-id="726e4-422">URL'yi yeniden yazmak için sunucuya gidiş-dönüş seyahat gerekmez.</span><span class="sxs-lookup"><span data-stu-id="726e4-422">Rewriting a URL doesn't require a round trip to the server.</span></span> <span data-ttu-id="726e4-423">Yeniden yazılan URL istemciye döndürülür ve tarayıcının adres çubuğunda görünmez.</span><span class="sxs-lookup"><span data-stu-id="726e4-423">The rewritten URL isn't returned to the client and doesn't appear in the browser's address bar.</span></span>

<span data-ttu-id="726e4-424">Yeniden `/resource` `/different-resource` *yazılırsa,* sunucu *kaynağı 'nda* `/different-resource`dahili olarak getirir ve döndürür.</span><span class="sxs-lookup"><span data-stu-id="726e4-424">If `/resource` is *rewritten* to `/different-resource`, the server *internally* fetches and returns the resource at `/different-resource`.</span></span>

<span data-ttu-id="726e4-425">İstemci kaynağı yeniden yazılmış URL'den alabilse de, istemci, isteği nde bulunduğunda ve yanıtı aldığında kaynağın yeniden yazılmış URL'de bulunduğu konusunda bilgilendirilmemektedir.</span><span class="sxs-lookup"><span data-stu-id="726e4-425">Although the client might be able to retrieve the resource at the rewritten URL, the client isn't informed that the resource exists at the rewritten URL when it makes its request and receives the response.</span></span>

![WebAPI hizmet bitiş noktası sunucudaki sürüm 1 'den (v1) sürüm 2 'ye (v2) değiştirildi.](url-rewriting/_static/url_rewrite.png)

## <a name="url-rewriting-sample-app"></a><span data-ttu-id="726e4-430">URL yeniden yazma örnek uygulaması</span><span class="sxs-lookup"><span data-stu-id="726e4-430">URL rewriting sample app</span></span>

<span data-ttu-id="726e4-431">[Örnek uygulama](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/)ile URL Rewriting Middleware özelliklerini keşfedebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="726e4-431">You can explore the features of the URL Rewriting Middleware with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/url-rewriting/samples/).</span></span> <span data-ttu-id="726e4-432">Uygulama yeniden yönlendirme ve yeniden yazma kuralları nı uygular ve çeşitli senaryolar için yeniden yönlendirilen veya yeniden yazılan URL'yi gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-432">The app applies redirect and rewrite rules and shows the redirected or rewritten URL for several scenarios.</span></span>

## <a name="when-to-use-url-rewriting-middleware"></a><span data-ttu-id="726e4-433">URL Rewriting Middleware ne zaman kullanılır</span><span class="sxs-lookup"><span data-stu-id="726e4-433">When to use URL Rewriting Middleware</span></span>

<span data-ttu-id="726e4-434">Aşağıdaki yaklaşımları kullanamadığınızda URL Yeniden Yazma Middleware'i kullanın:</span><span class="sxs-lookup"><span data-stu-id="726e4-434">Use URL Rewriting Middleware when you're unable to use the following approaches:</span></span>

* [<span data-ttu-id="726e4-435">Windows Server'da IIS ile URL Yeniden Yazma Modülü</span><span class="sxs-lookup"><span data-stu-id="726e4-435">URL Rewrite module with IIS on Windows Server</span></span>](https://www.iis.net/downloads/microsoft/url-rewrite)
* [<span data-ttu-id="726e4-436">Apache Server'da Apache mod_rewrite modülü</span><span class="sxs-lookup"><span data-stu-id="726e4-436">Apache mod_rewrite module on Apache Server</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="726e4-437">Nginx'te URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-437">URL rewriting on Nginx</span></span>](https://www.nginx.com/blog/creating-nginx-rewrite-rules/)

<span data-ttu-id="726e4-438">Ayrıca, uygulama [HTTP.sys sunucusunda](xref:fundamentals/servers/httpsys) (eski adıyla WebListener olarak adlandırılır) barındırıldığında ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-438">Also, use the middleware when the app is hosted on [HTTP.sys server](xref:fundamentals/servers/httpsys) (formerly called WebListener).</span></span>

<span data-ttu-id="726e4-439">IIS, Apache ve Nginx'te sunucu tabanlı URL yeniden yazma teknolojilerini kullanmanın başlıca nedenleri şunlardır:</span><span class="sxs-lookup"><span data-stu-id="726e4-439">The main reasons to use the server-based URL rewriting technologies in IIS, Apache, and Nginx are:</span></span>

* <span data-ttu-id="726e4-440">Ara yazılım bu modüllerin tüm özelliklerini desteklemez.</span><span class="sxs-lookup"><span data-stu-id="726e4-440">The middleware doesn't support the full features of these modules.</span></span>

  <span data-ttu-id="726e4-441">Sunucu modüllerinin bazı özellikleri, IIS Yeniden Yazma modülünün `IsFile` kısıtlamaları `IsDirectory` ve kısıtlamaları gibi ASP.NET Core projeleri ile çalışmaz.</span><span class="sxs-lookup"><span data-stu-id="726e4-441">Some of the features of the server modules don't work with ASP.NET Core projects, such as the `IsFile` and `IsDirectory` constraints of the IIS Rewrite module.</span></span> <span data-ttu-id="726e4-442">Bu senaryolarda, bunun yerine ara yazılımı kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-442">In these scenarios, use the middleware instead.</span></span>
* <span data-ttu-id="726e4-443">Ara yazılımın performansı muhtemelen modüllerin performansıyla eşleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="726e4-443">The performance of the middleware probably doesn't match that of the modules.</span></span>

  <span data-ttu-id="726e4-444">Kıyaslama, hangi yaklaşımın performansı en çok düşürdüğünü veya azalan performansın ihmal edilebilir olup olmadığını kesin olarak bilmenin tek yoludur.</span><span class="sxs-lookup"><span data-stu-id="726e4-444">Benchmarking is the only way to know for sure which approach degrades performance the most or if degraded performance is negligible.</span></span>

## <a name="package"></a><span data-ttu-id="726e4-445">Paket</span><span class="sxs-lookup"><span data-stu-id="726e4-445">Package</span></span>

<span data-ttu-id="726e4-446">Projenize ara yazılımı eklemek için, [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) paketini içeren proje dosyasına [Microsoft.AspNetCore.App metapaketine](xref:fundamentals/metapackage-app) bir paket başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-446">To include the middleware in your project, add a package reference to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in the project file, which contains the [Microsoft.AspNetCore.Rewrite](https://www.nuget.org/packages/Microsoft.AspNetCore.Rewrite) package.</span></span>

<span data-ttu-id="726e4-447">`Microsoft.AspNetCore.App` Meta paketi kullanmadığınızda, `Microsoft.AspNetCore.Rewrite` pakete bir proje başvurusu ekleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-447">When not using the `Microsoft.AspNetCore.App` metapackage, add a project reference to the `Microsoft.AspNetCore.Rewrite` package.</span></span>

## <a name="extension-and-options"></a><span data-ttu-id="726e4-448">Uzantı ve seçenekler</span><span class="sxs-lookup"><span data-stu-id="726e4-448">Extension and options</span></span>

<span data-ttu-id="726e4-449">Yeniden yazma kurallarınızın her biri için uzantı yöntemleriiçeren [Yeniden Yazma Seçenekleri](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) sınıfının bir örneğini oluşturarak URL yeniden yazma ve yeniden yönlendirme kuralları nı belirleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-449">Establish URL rewrite and redirect rules by creating an instance of the [RewriteOptions](xref:Microsoft.AspNetCore.Rewrite.RewriteOptions) class with extension methods for each of your rewrite rules.</span></span> <span data-ttu-id="726e4-450">İşlenmelerini istediğiniz sırayla birden çok kural zincirleyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-450">Chain multiple rules in the order that you would like them processed.</span></span> <span data-ttu-id="726e4-451">İstek `RewriteOptions` ardışık hattına eklenerek URL Yeniden Yazma Middleware'e <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>aktarılır:</span><span class="sxs-lookup"><span data-stu-id="726e4-451">The `RewriteOptions` are passed into the URL Rewriting Middleware as it's added to the request pipeline with <xref:Microsoft.AspNetCore.Builder.RewriteBuilderExtensions.UseRewriter*>:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

### <a name="redirect-non-www-to-www"></a><span data-ttu-id="726e4-452">Www olmayanları www'e yönlendirme</span><span class="sxs-lookup"><span data-stu-id="726e4-452">Redirect non-www to www</span></span>

<span data-ttu-id="726e4-453">Üç seçenek, uygulamanın olmayan`www` istekleri `www`aşağıdakilere yönlendirmesine izin verir:</span><span class="sxs-lookup"><span data-stu-id="726e4-453">Three options permit the app to redirect non-`www` requests to `www`:</span></span>

* <span data-ttu-id="726e4-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*>&ndash; İstek yoksa, isteği kalıcı `www` olarak alt etki alanına`www`yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="726e4-454"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWwwPermanent*> &ndash; Permanently redirect the request to the `www` subdomain if the request is non-`www`.</span></span> <span data-ttu-id="726e4-455">[Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) durum koduyla yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-455">Redirects with a [Status308PermanentRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status308PermanentRedirect) status code.</span></span>

* <span data-ttu-id="726e4-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*>&ndash; Gelen istek yoksa, isteği `www` alt etki alanına`www`yönlendirin.</span><span class="sxs-lookup"><span data-stu-id="726e4-456"><xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToWww*> &ndash; Redirect the request to the `www` subdomain if the incoming request is non-`www`.</span></span> <span data-ttu-id="726e4-457">[Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) durum koduyla yeniden yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-457">Redirects with a [Status307TemporaryRedirect](xref:Microsoft.AspNetCore.Http.StatusCodes.Status307TemporaryRedirect) status code.</span></span> <span data-ttu-id="726e4-458">Aşırı yükleme, yanıtın durum kodunu sağlamanıza izin verir.</span><span class="sxs-lookup"><span data-stu-id="726e4-458">An overload permits you to provide the status code for the response.</span></span> <span data-ttu-id="726e4-459">Durum kodu ataması <xref:Microsoft.AspNetCore.Http.StatusCodes> için sınıfın alanını kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-459">Use a field of the <xref:Microsoft.AspNetCore.Http.StatusCodes> class for a status code assignment.</span></span>

### <a name="url-redirect"></a><span data-ttu-id="726e4-460">URL yeniden yönlendirme</span><span class="sxs-lookup"><span data-stu-id="726e4-460">URL redirect</span></span>

<span data-ttu-id="726e4-461">İstekleri yönlendirmek için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*></span><span class="sxs-lookup"><span data-stu-id="726e4-461">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirect*> to redirect requests.</span></span> <span data-ttu-id="726e4-462">İlk parametre, gelen URL'nin yolunda eşleştirme için regex'inizi içerir.</span><span class="sxs-lookup"><span data-stu-id="726e4-462">The first parameter contains your regex for matching on the path of the incoming URL.</span></span> <span data-ttu-id="726e4-463">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-463">The second parameter is the replacement string.</span></span> <span data-ttu-id="726e4-464">Üçüncü parametre, varsa durum kodunu belirtir.</span><span class="sxs-lookup"><span data-stu-id="726e4-464">The third parameter, if present, specifies the status code.</span></span> <span data-ttu-id="726e4-465">Durum kodunu belirtmezseniz, durum kodu varsayılan *olarak 302 - Found*, kaynağın geçici olarak taşındığını veya değiştirilmediğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-465">If you don't specify the status code, the status code defaults to *302 - Found*, which indicates that the resource is temporarily moved or replaced.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=9)]

<span data-ttu-id="726e4-466">Geliştirici araçları etkin leştirilmiş bir tarayıcıda, yol `/redirect-rule/1234/5678`ile örnek uygulama için bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="726e4-466">In a browser with developer tools enabled, make a request to the sample app with the path `/redirect-rule/1234/5678`.</span></span> <span data-ttu-id="726e4-467">Regex, istek yolu `redirect-rule/(.*)`üzerinde eşleşir ve yol `/redirected/1234/5678`' ile değiştirilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-467">The regex matches the request path on `redirect-rule/(.*)`, and the path is replaced with `/redirected/1234/5678`.</span></span> <span data-ttu-id="726e4-468">Yönlendirme URL'si *302 - Found* durum koduyla istemciye geri gönderilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-468">The redirect URL is sent back to the client with a *302 - Found* status code.</span></span> <span data-ttu-id="726e4-469">Tarayıcı, tarayıcının adres çubuğunda görünen yönlendirme URL'sinde yeni bir istekte bulunr.</span><span class="sxs-lookup"><span data-stu-id="726e4-469">The browser makes a new request at the redirect URL, which appears in the browser's address bar.</span></span> <span data-ttu-id="726e4-470">Örnek uygulamadaki kural yönlendirme URL'sinde eşleşmediğinden:</span><span class="sxs-lookup"><span data-stu-id="726e4-470">Since no rules in the sample app match on the redirect URL:</span></span>

* <span data-ttu-id="726e4-471">İkinci istek uygulamadan *200 - Tamam* yanıtı alır.</span><span class="sxs-lookup"><span data-stu-id="726e4-471">The second request receives a *200 - OK* response from the app.</span></span>
* <span data-ttu-id="726e4-472">Yanıtın gövdesi yönlendirme URL'sini gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-472">The body of the response shows the redirect URL.</span></span>

<span data-ttu-id="726e4-473">URL *yönlendirildiğinde*sunucuya gidiş-dönüş yapılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-473">A round trip is made to the server when a URL is *redirected*.</span></span>

> [!WARNING]
> <span data-ttu-id="726e4-474">Yönlendirme kuralları oluştururken dikkatli olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-474">Be cautious when establishing redirect rules.</span></span> <span data-ttu-id="726e4-475">Yönlendirme kuralları, yönlendirme sonrası da dahil olmak üzere uygulamaya yapılan her istekte değerlendirilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-475">Redirect rules are evaluated on every request to the app, including after a redirect.</span></span> <span data-ttu-id="726e4-476">Yanlışlıkla *sonsuz yönlendirmeler*bir döngü oluşturmak kolaydır.</span><span class="sxs-lookup"><span data-stu-id="726e4-476">It's easy to accidentally create a *loop of infinite redirects*.</span></span>

<span data-ttu-id="726e4-477">Orijinal İstek:`/redirect-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="726e4-477">Original Request: `/redirect-rule/1234/5678`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect.png)

<span data-ttu-id="726e4-479">İfadenin parantez içinde bulunan bölümüne *yakalama grubu*denir.</span><span class="sxs-lookup"><span data-stu-id="726e4-479">The part of the expression contained within parentheses is called a *capture group*.</span></span> <span data-ttu-id="726e4-480">İfadenin nokta`.`( ) *herhangi bir karakterle eşleşmesi*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="726e4-480">The dot (`.`) of the expression means *match any character*.</span></span> <span data-ttu-id="726e4-481">Yıldız işareti (`*`) *önceki karakter sıfır veya daha fazla kez eşleşir.*</span><span class="sxs-lookup"><span data-stu-id="726e4-481">The asterisk (`*`) indicates *match the preceding character zero or more times*.</span></span> <span data-ttu-id="726e4-482">Bu nedenle, URL'nin son iki `1234/5678`yol kesimi, `(.*)`yakalama grubu tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-482">Therefore, the last two path segments of the URL, `1234/5678`, are captured by capture group `(.*)`.</span></span> <span data-ttu-id="726e4-483">İstek URL'sinde `redirect-rule/` sağladığınız herhangi bir değer bu tek yakalama grubu tarafından yakalanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-483">Any value you provide in the request URL after `redirect-rule/` is captured by this single capture group.</span></span>

<span data-ttu-id="726e4-484">Yedek dizede, yakalanan gruplar dolar işareti ile dize içine enjekte edilir (`$`) yakalama sıra numarası takip.</span><span class="sxs-lookup"><span data-stu-id="726e4-484">In the replacement string, captured groups are injected into the string with the dollar sign (`$`) followed by the sequence number of the capture.</span></span> <span data-ttu-id="726e4-485">İlk yakalama grubu değeri `$1`, ikinci `$2`ile elde edilir ve regex yakalama grupları için sırayla devam eder.</span><span class="sxs-lookup"><span data-stu-id="726e4-485">The first capture group value is obtained with `$1`, the second with `$2`, and they continue in sequence for the capture groups in your regex.</span></span> <span data-ttu-id="726e4-486">Örnek uygulamasında yönlendirme kuralı regex'te yakalanan tek bir grup var. `$1`</span><span class="sxs-lookup"><span data-stu-id="726e4-486">There's only one captured group in the redirect rule regex in the sample app, so there's only one injected group in the replacement string, which is `$1`.</span></span> <span data-ttu-id="726e4-487">Kural uygulandığında, URL olur. `/redirected/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="726e4-487">When the rule is applied, the URL becomes `/redirected/1234/5678`.</span></span>

### <a name="url-redirect-to-a-secure-endpoint"></a><span data-ttu-id="726e4-488">URL güvenli bir bitiş noktasına yönlendirilir</span><span class="sxs-lookup"><span data-stu-id="726e4-488">URL redirect to a secure endpoint</span></span>

<span data-ttu-id="726e4-489">HTTPS <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> protokolünü kullanarak HTTP isteklerini aynı ana bilgisayara ve yola yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-489">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttps*> to redirect HTTP requests to the same host and path using the HTTPS protocol.</span></span> <span data-ttu-id="726e4-490">Durum kodu sağlanmıyorsa, ara yazılım varsayılan *olarak 302 - Bulundu.*</span><span class="sxs-lookup"><span data-stu-id="726e4-490">If the status code isn't supplied, the middleware defaults to *302 - Found*.</span></span> <span data-ttu-id="726e4-491">Bağlantı noktası sağlanmıyorsa:</span><span class="sxs-lookup"><span data-stu-id="726e4-491">If the port isn't supplied:</span></span>

* <span data-ttu-id="726e4-492">Ara yazılım varsayılan `null`olarak .</span><span class="sxs-lookup"><span data-stu-id="726e4-492">The middleware defaults to `null`.</span></span>
* <span data-ttu-id="726e4-493">Şema (HTTPS protokolü) olarak `https` değişir ve istemci bağlantı noktası 443'teki kaynağa erişer.</span><span class="sxs-lookup"><span data-stu-id="726e4-493">The scheme changes to `https` (HTTPS protocol), and the client accesses the resource on port 443.</span></span>

<span data-ttu-id="726e4-494">Aşağıdaki örnek, durum *kodunun 301 - Moved Permanent'e* nasıl ayarlanır ve bağlantı noktasını 5001 olarak nasıl değiştireceğini gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-494">The following example shows how to set the status code to *301 - Moved Permanently* and change the port to 5001.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttps(301, 5001);

    app.UseRewriter(options);
}
```

<span data-ttu-id="726e4-495">Güvenli <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> olmayan istekleri aynı ana bilgisayara ve bağlantı noktası 443'teki güvenli HTTPS protokolüyle yola yönlendirmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-495">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRedirectToHttpsPermanent*> to redirect insecure requests to the same host and path with secure HTTPS protocol on port 443.</span></span> <span data-ttu-id="726e4-496">Ara yazılım durum kodunu *301*olarak ayarlar - Kalıcı olarak taşınır.</span><span class="sxs-lookup"><span data-stu-id="726e4-496">The middleware sets the status code to *301 - Moved Permanently*.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    var options = new RewriteOptions()
        .AddRedirectToHttpsPermanent();

    app.UseRewriter(options);
}
```

> [!NOTE]
> <span data-ttu-id="726e4-497">Ek yeniden yönlendirme kuralları gerekmeden güvenli bir bitiş noktasına yönlendirirken, HTTPS Yönlendirme Middleware'i kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="726e4-497">When redirecting to a secure endpoint without the requirement for additional redirect rules, we recommend using HTTPS Redirection Middleware.</span></span> <span data-ttu-id="726e4-498">Daha fazla bilgi için [HTTPS'yi Uygula](xref:security/enforcing-ssl#require-https) konusuna bakın.</span><span class="sxs-lookup"><span data-stu-id="726e4-498">For more information, see the [Enforce HTTPS](xref:security/enforcing-ssl#require-https) topic.</span></span>

<span data-ttu-id="726e4-499">Örnek uygulama nasıl kullanılacağını veya `AddRedirectToHttps` `AddRedirectToHttpsPermanent`nasıl kullanılacağını gösterebilme yeteneğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="726e4-499">The sample app is capable of demonstrating how to use `AddRedirectToHttps` or `AddRedirectToHttpsPermanent`.</span></span> <span data-ttu-id="726e4-500">Uzantı yöntemini `RewriteOptions`.</span><span class="sxs-lookup"><span data-stu-id="726e4-500">Add the extension method to the `RewriteOptions`.</span></span> <span data-ttu-id="726e4-501">Herhangi bir URL'den uygulamaya güvenli olmayan bir istekte bulunun.</span><span class="sxs-lookup"><span data-stu-id="726e4-501">Make an insecure request to the app at any URL.</span></span> <span data-ttu-id="726e4-502">Tarayıcı güvenlik uyarısını, kendi imzaladığı sertifikanın güvenilen olmadığı konusunda görevden alın veya sertifikaya güvenmek için bir özel durum oluşturun.</span><span class="sxs-lookup"><span data-stu-id="726e4-502">Dismiss the browser security warning that the self-signed certificate is untrusted or create an exception to trust the certificate.</span></span>

<span data-ttu-id="726e4-503">Orijinal İstek `AddRedirectToHttps(301, 5001)`kullanarak:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="726e4-503">Original Request using `AddRedirectToHttps(301, 5001)`: `http://localhost:5000/secure`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https.png)

<span data-ttu-id="726e4-505">Orijinal İstek `AddRedirectToHttpsPermanent`kullanarak:`http://localhost:5000/secure`</span><span class="sxs-lookup"><span data-stu-id="726e4-505">Original Request using `AddRedirectToHttpsPermanent`: `http://localhost:5000/secure`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_redirect_to_https_permanent.png)

### <a name="url-rewrite"></a><span data-ttu-id="726e4-507">URL yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-507">URL rewrite</span></span>

<span data-ttu-id="726e4-508">URL'leri yeniden yazmak için bir kural oluşturmak için kullanın. <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*></span><span class="sxs-lookup"><span data-stu-id="726e4-508">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.AddRewrite*> to create a rule for rewriting URLs.</span></span> <span data-ttu-id="726e4-509">İlk parametre, gelen URL yolunda eşleştirme için regex içerir.</span><span class="sxs-lookup"><span data-stu-id="726e4-509">The first parameter contains the regex for matching on the incoming URL path.</span></span> <span data-ttu-id="726e4-510">İkinci parametre değiştirme dizesidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-510">The second parameter is the replacement string.</span></span> <span data-ttu-id="726e4-511">Üçüncü parametre, `skipRemainingRules: {true|false}`geçerli kural uygulanırsa ek yeniden yazma kuralları atlamak olsun veya olmasın middleware gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-511">The third parameter, `skipRemainingRules: {true|false}`, indicates to the middleware whether or not to skip additional rewrite rules if the current rule is applied.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=10-11)]

<span data-ttu-id="726e4-512">Orijinal İstek:`/rewrite-rule/1234/5678`</span><span class="sxs-lookup"><span data-stu-id="726e4-512">Original Request: `/rewrite-rule/1234/5678`</span></span>

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_rewrite.png)

<span data-ttu-id="726e4-514">İfadenin başındaki karat (`^`) eşleştirmenin URL yolunun başında başladığı anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="726e4-514">The carat (`^`) at the beginning of the expression means that matching starts at the beginning of the URL path.</span></span>

<span data-ttu-id="726e4-515">Yönlendirme kuralı ile önceki örnekte, `redirect-rule/(.*)`regex başında hiçbir`^`karat () var.</span><span class="sxs-lookup"><span data-stu-id="726e4-515">In the earlier example with the redirect rule, `redirect-rule/(.*)`, there's no carat (`^`) at the start of the regex.</span></span> <span data-ttu-id="726e4-516">Bu nedenle, herhangi bir `redirect-rule/` karakter başarılı bir eşleşme için yolda önce olabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-516">Therefore, any characters may precede `redirect-rule/` in the path for a successful match.</span></span>

| <span data-ttu-id="726e4-517">Yol</span><span class="sxs-lookup"><span data-stu-id="726e4-517">Path</span></span>                               | <span data-ttu-id="726e4-518">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="726e4-518">Match</span></span> |
| ---------------------------------- | :---: |
| `/redirect-rule/1234/5678`         | <span data-ttu-id="726e4-519">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-519">Yes</span></span>   |
| `/my-cool-redirect-rule/1234/5678` | <span data-ttu-id="726e4-520">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-520">Yes</span></span>   |
| `/anotherredirect-rule/1234/5678`  | <span data-ttu-id="726e4-521">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-521">Yes</span></span>   |

<span data-ttu-id="726e4-522">Yeniden yazma kuralı, `^rewrite-rule/(\d+)/(\d+)`yalnızca ' ile `rewrite-rule/`başlarlarsa yollar eşleşir.</span><span class="sxs-lookup"><span data-stu-id="726e4-522">The rewrite rule, `^rewrite-rule/(\d+)/(\d+)`, only matches paths if they start with `rewrite-rule/`.</span></span> <span data-ttu-id="726e4-523">Aşağıdaki tabloda, eşleştirme farkı dikkat edin.</span><span class="sxs-lookup"><span data-stu-id="726e4-523">In the following table, note the difference in matching.</span></span>

| <span data-ttu-id="726e4-524">Yol</span><span class="sxs-lookup"><span data-stu-id="726e4-524">Path</span></span>                              | <span data-ttu-id="726e4-525">Eşleştirme</span><span class="sxs-lookup"><span data-stu-id="726e4-525">Match</span></span> |
| --------------------------------- | :---: |
| `/rewrite-rule/1234/5678`         | <span data-ttu-id="726e4-526">Evet</span><span class="sxs-lookup"><span data-stu-id="726e4-526">Yes</span></span>   |
| `/my-cool-rewrite-rule/1234/5678` | <span data-ttu-id="726e4-527">Hayır</span><span class="sxs-lookup"><span data-stu-id="726e4-527">No</span></span>    |
| `/anotherrewrite-rule/1234/5678`  | <span data-ttu-id="726e4-528">Hayır</span><span class="sxs-lookup"><span data-stu-id="726e4-528">No</span></span>    |

<span data-ttu-id="726e4-529">İfadenin `^rewrite-rule/` bir bölümünü takiben, iki `(\d+)/(\d+)`yakalama grubu vardır.</span><span class="sxs-lookup"><span data-stu-id="726e4-529">Following the `^rewrite-rule/` portion of the expression, there are two capture groups, `(\d+)/(\d+)`.</span></span> <span data-ttu-id="726e4-530">Bir `\d` basamak *(sayı) eşleşmesi*anlamına gelen.</span><span class="sxs-lookup"><span data-stu-id="726e4-530">The `\d` signifies *match a digit (number)*.</span></span> <span data-ttu-id="726e4-531">Artı işareti`+`( ) *önceki karakterin bir veya daha fazla eşleşmesi*anlamına gelir.</span><span class="sxs-lookup"><span data-stu-id="726e4-531">The plus sign (`+`) means *match one or more of the preceding character*.</span></span> <span data-ttu-id="726e4-532">Bu nedenle, URL bir sayı ve ardından başka bir sayı takip eden bir sayı içermelidir.</span><span class="sxs-lookup"><span data-stu-id="726e4-532">Therefore, the URL must contain a number followed by a forward-slash followed by another number.</span></span> <span data-ttu-id="726e4-533">Bu yakalama grupları yeniden yazılan URL'ye `$2`. `$1`</span><span class="sxs-lookup"><span data-stu-id="726e4-533">These capture groups are injected into the rewritten URL as `$1` and `$2`.</span></span> <span data-ttu-id="726e4-534">Yeniden yazma kuralı değiştirme dizesi, yakalanan grupları sorgu dizesine yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-534">The rewrite rule replacement string places the captured groups into the query string.</span></span> <span data-ttu-id="726e4-535">İstenen `/rewrite-rule/1234/5678` yol, kaynağı n ' `/rewritten?var1=1234&var2=5678`den elde etmek için yeniden yazılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-535">The requested path of `/rewrite-rule/1234/5678` is rewritten to obtain the resource at `/rewritten?var1=1234&var2=5678`.</span></span> <span data-ttu-id="726e4-536">Özgün istekte bir sorgu dizesi varsa, URL yeniden yazıldığında korunur.</span><span class="sxs-lookup"><span data-stu-id="726e4-536">If a query string is present on the original request, it's preserved when the URL is rewritten.</span></span>

<span data-ttu-id="726e4-537">Kaynağı elde etmek için sunucuya gidiş dönüş yok.</span><span class="sxs-lookup"><span data-stu-id="726e4-537">There's no round trip to the server to obtain the resource.</span></span> <span data-ttu-id="726e4-538">Kaynak varsa, *200 - Tamam* durum koduyla getirilir ve istemciye döndürülür.</span><span class="sxs-lookup"><span data-stu-id="726e4-538">If the resource exists, it's fetched and returned to the client with a *200 - OK* status code.</span></span> <span data-ttu-id="726e4-539">İstemci yönlendirilmediği için tarayıcının adres çubuğundaki URL değişmez.</span><span class="sxs-lookup"><span data-stu-id="726e4-539">Because the client isn't redirected, the URL in the browser's address bar doesn't change.</span></span> <span data-ttu-id="726e4-540">İstemciler, sunucuda bir URL yeniden yazma işleminin gerçekleştiğini algılayamamaktadır.</span><span class="sxs-lookup"><span data-stu-id="726e4-540">Clients can't detect that a URL rewrite operation occurred on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="726e4-541">Eşleşen `skipRemainingRules: true` kurallar hesaplama olarak pahalı olduğundan ve uygulama yanıt süresini artırdığından, mümkün olduğunca kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-541">Use `skipRemainingRules: true` whenever possible because matching rules is computationally expensive and increases app response time.</span></span> <span data-ttu-id="726e4-542">En hızlı uygulama yanıtı için:</span><span class="sxs-lookup"><span data-stu-id="726e4-542">For the fastest app response:</span></span>
>
> * <span data-ttu-id="726e4-543">En sık eşleşen kuraldan en sık eşleşen kurala yeniden yazma kurallarını sırala.</span><span class="sxs-lookup"><span data-stu-id="726e4-543">Order rewrite rules from the most frequently matched rule to the least frequently matched rule.</span></span>
> * <span data-ttu-id="726e4-544">Eşleşme oluştuğunda ve ek kural işleme gerektirildiğinde kalan kuralların işlenmesini atlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-544">Skip the processing of the remaining rules when a match occurs and no additional rule processing is required.</span></span>

### <a name="apache-mod_rewrite"></a><span data-ttu-id="726e4-545">Apaçi mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="726e4-545">Apache mod_rewrite</span></span>

<span data-ttu-id="726e4-546">Apache mod_rewrite kurallarını <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span><span class="sxs-lookup"><span data-stu-id="726e4-546">Apply Apache mod_rewrite rules with <xref:Microsoft.AspNetCore.Rewrite.ApacheModRewriteOptionsExtensions.AddApacheModRewrite*>.</span></span> <span data-ttu-id="726e4-547">Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-547">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="726e4-548">Daha fazla bilgi ve mod_rewrite kuralları örnekleri için, [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/)bakın.</span><span class="sxs-lookup"><span data-stu-id="726e4-548">For more information and examples of mod_rewrite rules, see [Apache mod_rewrite](https://httpd.apache.org/docs/2.4/rewrite/).</span></span>

<span data-ttu-id="726e4-549"><xref:System.IO.StreamReader> A, *ApacheModRewrite.txt* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="726e4-549">A <xref:System.IO.StreamReader> is used to read the rules from the *ApacheModRewrite.txt* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=3-4,12)]

<span data-ttu-id="726e4-550">Örnek uygulama istekleri ' `/apache-mod-rules-redirect/(.\*)` den `/redirected?id=$1`' e yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-550">The sample app redirects requests from `/apache-mod-rules-redirect/(.\*)` to `/redirected?id=$1`.</span></span> <span data-ttu-id="726e4-551">Yanıt durum kodu *302 - Bulundu.*</span><span class="sxs-lookup"><span data-stu-id="726e4-551">The response status code is *302 - Found*.</span></span>

[!code[](url-rewriting/samples/2.x/SampleApp/ApacheModRewrite.txt)]

<span data-ttu-id="726e4-552">Orijinal İstek:`/apache-mod-rules-redirect/1234`</span><span class="sxs-lookup"><span data-stu-id="726e4-552">Original Request: `/apache-mod-rules-redirect/1234`</span></span>

![Geliştirici Araçları'nın istekleri ve yanıtları izlediği tarayıcı penceresi](url-rewriting/_static/add_apache_mod_redirect.png)

<span data-ttu-id="726e4-554">Ara yazılım aşağıdaki Apache mod_rewrite sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="726e4-554">The middleware supports the following Apache mod_rewrite server variables:</span></span>

* <span data-ttu-id="726e4-555">CONN_REMOTE_ADDR</span><span class="sxs-lookup"><span data-stu-id="726e4-555">CONN_REMOTE_ADDR</span></span>
* <span data-ttu-id="726e4-556">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="726e4-556">HTTP_ACCEPT</span></span>
* <span data-ttu-id="726e4-557">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="726e4-557">HTTP_CONNECTION</span></span>
* <span data-ttu-id="726e4-558">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="726e4-558">HTTP_COOKIE</span></span>
* <span data-ttu-id="726e4-559">HTTP_FORWARDED</span><span class="sxs-lookup"><span data-stu-id="726e4-559">HTTP_FORWARDED</span></span>
* <span data-ttu-id="726e4-560">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="726e4-560">HTTP_HOST</span></span>
* <span data-ttu-id="726e4-561">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="726e4-561">HTTP_REFERER</span></span>
* <span data-ttu-id="726e4-562">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="726e4-562">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="726e4-563">HTTPS</span><span class="sxs-lookup"><span data-stu-id="726e4-563">HTTPS</span></span>
* <span data-ttu-id="726e4-564">IPV6</span><span class="sxs-lookup"><span data-stu-id="726e4-564">IPV6</span></span>
* <span data-ttu-id="726e4-565">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="726e4-565">QUERY_STRING</span></span>
* <span data-ttu-id="726e4-566">Remote_addr</span><span class="sxs-lookup"><span data-stu-id="726e4-566">REMOTE_ADDR</span></span>
* <span data-ttu-id="726e4-567">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="726e4-567">REMOTE_PORT</span></span>
* <span data-ttu-id="726e4-568">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="726e4-568">REQUEST_FILENAME</span></span>
* <span data-ttu-id="726e4-569">REQUEST_METHOD</span><span class="sxs-lookup"><span data-stu-id="726e4-569">REQUEST_METHOD</span></span>
* <span data-ttu-id="726e4-570">REQUEST_SCHEME</span><span class="sxs-lookup"><span data-stu-id="726e4-570">REQUEST_SCHEME</span></span>
* <span data-ttu-id="726e4-571">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="726e4-571">REQUEST_URI</span></span>
* <span data-ttu-id="726e4-572">SCRIPT_FILENAME</span><span class="sxs-lookup"><span data-stu-id="726e4-572">SCRIPT_FILENAME</span></span>
* <span data-ttu-id="726e4-573">SERVER_ADDR</span><span class="sxs-lookup"><span data-stu-id="726e4-573">SERVER_ADDR</span></span>
* <span data-ttu-id="726e4-574">SERVER_PORT</span><span class="sxs-lookup"><span data-stu-id="726e4-574">SERVER_PORT</span></span>
* <span data-ttu-id="726e4-575">SERVER_PROTOCOL</span><span class="sxs-lookup"><span data-stu-id="726e4-575">SERVER_PROTOCOL</span></span>
* <span data-ttu-id="726e4-576">TIME</span><span class="sxs-lookup"><span data-stu-id="726e4-576">TIME</span></span>
* <span data-ttu-id="726e4-577">TIME_DAY</span><span class="sxs-lookup"><span data-stu-id="726e4-577">TIME_DAY</span></span>
* <span data-ttu-id="726e4-578">TIME_HOUR</span><span class="sxs-lookup"><span data-stu-id="726e4-578">TIME_HOUR</span></span>
* <span data-ttu-id="726e4-579">TIME_MIN</span><span class="sxs-lookup"><span data-stu-id="726e4-579">TIME_MIN</span></span>
* <span data-ttu-id="726e4-580">TIME_MON</span><span class="sxs-lookup"><span data-stu-id="726e4-580">TIME_MON</span></span>
* <span data-ttu-id="726e4-581">TIME_SEC</span><span class="sxs-lookup"><span data-stu-id="726e4-581">TIME_SEC</span></span>
* <span data-ttu-id="726e4-582">TIME_WDAY</span><span class="sxs-lookup"><span data-stu-id="726e4-582">TIME_WDAY</span></span>
* <span data-ttu-id="726e4-583">TIME_YEAR</span><span class="sxs-lookup"><span data-stu-id="726e4-583">TIME_YEAR</span></span>

### <a name="iis-url-rewrite-module-rules"></a><span data-ttu-id="726e4-584">IIS URL Yeniden Yazma Modülü kuralları</span><span class="sxs-lookup"><span data-stu-id="726e4-584">IIS URL Rewrite Module rules</span></span>

<span data-ttu-id="726e4-585">IIS URL Yeniden Yazma Modülü için geçerli olan aynı <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>kural kümesini kullanmak için .</span><span class="sxs-lookup"><span data-stu-id="726e4-585">To use the same rule set that applies to the IIS URL Rewrite Module, use <xref:Microsoft.AspNetCore.Rewrite.IISUrlRewriteOptionsExtensions.AddIISUrlRewrite*>.</span></span> <span data-ttu-id="726e4-586">Kurallar dosyasının uygulamayla birlikte dağıtıldıkdığından emin olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-586">Make sure that the rules file is deployed with the app.</span></span> <span data-ttu-id="726e4-587">Windows Server IIS'de çalışırken ara yazılımı uygulamanın *web.config* dosyasını kullanmaya yönlendirmeyin.</span><span class="sxs-lookup"><span data-stu-id="726e4-587">Don't direct the middleware to use the app's *web.config* file when running on Windows Server IIS.</span></span> <span data-ttu-id="726e4-588">IIS ile bu kurallar, IIS Yeniden Yazma modülüyle çakışmaları önlemek için uygulamanın *web.config* dosyasının dışında saklanmalıdır.</span><span class="sxs-lookup"><span data-stu-id="726e4-588">With IIS, these rules should be stored outside of the app's *web.config* file in order to avoid conflicts with the IIS Rewrite module.</span></span> <span data-ttu-id="726e4-589">IIS URL Yeniden Yazma Modülü kuralları hakkında daha fazla bilgi [ve](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) örnekler için [bkz.](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)</span><span class="sxs-lookup"><span data-stu-id="726e4-589">For more information and examples of IIS URL Rewrite Module rules, see [Using Url Rewrite Module 2.0](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20) and [URL Rewrite Module Configuration Reference](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference).</span></span>

<span data-ttu-id="726e4-590"><xref:System.IO.StreamReader> A, *IISUrlRewrite.xml* kuralları dosyasındaki kuralları okumak için kullanılır:</span><span class="sxs-lookup"><span data-stu-id="726e4-590">A <xref:System.IO.StreamReader> is used to read the rules from the *IISUrlRewrite.xml* rules file:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=5-6,13)]

<span data-ttu-id="726e4-591">Örnek uygulama isteklerini `/iis-rules-rewrite/(.*)` 'den ' e `/rewritten?id=$1`yeniden yazar</span><span class="sxs-lookup"><span data-stu-id="726e4-591">The sample app rewrites requests from `/iis-rules-rewrite/(.*)` to `/rewritten?id=$1`.</span></span> <span data-ttu-id="726e4-592">Yanıt istemciye *200 - Tamam* durum koduyla gönderilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-592">The response is sent to the client with a *200 - OK* status code.</span></span>

[!code-xml[](url-rewriting/samples/2.x/SampleApp/IISUrlRewrite.xml)]

<span data-ttu-id="726e4-593">Orijinal İstek:`/iis-rules-rewrite/1234`</span><span class="sxs-lookup"><span data-stu-id="726e4-593">Original Request: `/iis-rules-rewrite/1234`</span></span>

![Geliştirici Araçları'nın isteği ve yanıtı izlediği tarayıcı penceresi](url-rewriting/_static/add_iis_url_rewrite.png)

<span data-ttu-id="726e4-595">Uygulamanızı istenmeyen şekillerde etkileyecek şekilde yapılandırılan sunucu düzeyinde kurallara sahip etkin bir IIS Yeniden Yazma Modülünüz varsa, bir uygulama için IIS Yeniden Yazma Modül'ünden devre dışı kullanabilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="726e4-595">If you have an active IIS Rewrite Module with server-level rules configured that would impact your app in undesirable ways, you can disable the IIS Rewrite Module for an app.</span></span> <span data-ttu-id="726e4-596">Daha fazla bilgi için Bkz. [IIS modüllerini devre dışı bırakma.](xref:host-and-deploy/iis/modules#disabling-iis-modules)</span><span class="sxs-lookup"><span data-stu-id="726e4-596">For more information, see [Disabling IIS modules](xref:host-and-deploy/iis/modules#disabling-iis-modules).</span></span>

#### <a name="unsupported-features"></a><span data-ttu-id="726e4-597">Desteklenmeyen özellikler</span><span class="sxs-lookup"><span data-stu-id="726e4-597">Unsupported features</span></span>

<span data-ttu-id="726e4-598">Core 2.x ile ASP.NET yayımlanan ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü özelliklerini desteklemez:</span><span class="sxs-lookup"><span data-stu-id="726e4-598">The middleware released with ASP.NET Core 2.x doesn't support the following IIS URL Rewrite Module features:</span></span>

* <span data-ttu-id="726e4-599">Giden Kuralları</span><span class="sxs-lookup"><span data-stu-id="726e4-599">Outbound Rules</span></span>
* <span data-ttu-id="726e4-600">Özel Sunucu Değişkenleri</span><span class="sxs-lookup"><span data-stu-id="726e4-600">Custom Server Variables</span></span>
* <span data-ttu-id="726e4-601">Joker karakterler</span><span class="sxs-lookup"><span data-stu-id="726e4-601">Wildcards</span></span>
* <span data-ttu-id="726e4-602">LogRewrittenUrl</span><span class="sxs-lookup"><span data-stu-id="726e4-602">LogRewrittenUrl</span></span>

#### <a name="supported-server-variables"></a><span data-ttu-id="726e4-603">Desteklenen sunucu değişkenleri</span><span class="sxs-lookup"><span data-stu-id="726e4-603">Supported server variables</span></span>

<span data-ttu-id="726e4-604">Ara yazılım aşağıdaki IIS URL Yeniden Yazma Modülü sunucu değişkenlerini destekler:</span><span class="sxs-lookup"><span data-stu-id="726e4-604">The middleware supports the following IIS URL Rewrite Module server variables:</span></span>

* <span data-ttu-id="726e4-605">CONTENT_LENGTH</span><span class="sxs-lookup"><span data-stu-id="726e4-605">CONTENT_LENGTH</span></span>
* <span data-ttu-id="726e4-606">CONTENT_TYPE</span><span class="sxs-lookup"><span data-stu-id="726e4-606">CONTENT_TYPE</span></span>
* <span data-ttu-id="726e4-607">HTTP_ACCEPT</span><span class="sxs-lookup"><span data-stu-id="726e4-607">HTTP_ACCEPT</span></span>
* <span data-ttu-id="726e4-608">HTTP_CONNECTION</span><span class="sxs-lookup"><span data-stu-id="726e4-608">HTTP_CONNECTION</span></span>
* <span data-ttu-id="726e4-609">HTTP_COOKIE</span><span class="sxs-lookup"><span data-stu-id="726e4-609">HTTP_COOKIE</span></span>
* <span data-ttu-id="726e4-610">HTTP_HOST</span><span class="sxs-lookup"><span data-stu-id="726e4-610">HTTP_HOST</span></span>
* <span data-ttu-id="726e4-611">HTTP_REFERER</span><span class="sxs-lookup"><span data-stu-id="726e4-611">HTTP_REFERER</span></span>
* <span data-ttu-id="726e4-612">HTTP_URL</span><span class="sxs-lookup"><span data-stu-id="726e4-612">HTTP_URL</span></span>
* <span data-ttu-id="726e4-613">HTTP_USER_AGENT</span><span class="sxs-lookup"><span data-stu-id="726e4-613">HTTP_USER_AGENT</span></span>
* <span data-ttu-id="726e4-614">HTTPS</span><span class="sxs-lookup"><span data-stu-id="726e4-614">HTTPS</span></span>
* <span data-ttu-id="726e4-615">LOCAL_ADDR</span><span class="sxs-lookup"><span data-stu-id="726e4-615">LOCAL_ADDR</span></span>
* <span data-ttu-id="726e4-616">QUERY_STRING</span><span class="sxs-lookup"><span data-stu-id="726e4-616">QUERY_STRING</span></span>
* <span data-ttu-id="726e4-617">Remote_addr</span><span class="sxs-lookup"><span data-stu-id="726e4-617">REMOTE_ADDR</span></span>
* <span data-ttu-id="726e4-618">REMOTE_PORT</span><span class="sxs-lookup"><span data-stu-id="726e4-618">REMOTE_PORT</span></span>
* <span data-ttu-id="726e4-619">REQUEST_FILENAME</span><span class="sxs-lookup"><span data-stu-id="726e4-619">REQUEST_FILENAME</span></span>
* <span data-ttu-id="726e4-620">REQUEST_URI</span><span class="sxs-lookup"><span data-stu-id="726e4-620">REQUEST_URI</span></span>

> [!NOTE]
> <span data-ttu-id="726e4-621">Ayrıca bir <xref:Microsoft.Extensions.FileProviders.IFileProvider> <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>üzerinden elde edebilirsiniz .</span><span class="sxs-lookup"><span data-stu-id="726e4-621">You can also obtain an <xref:Microsoft.Extensions.FileProviders.IFileProvider> via a <xref:Microsoft.Extensions.FileProviders.PhysicalFileProvider>.</span></span> <span data-ttu-id="726e4-622">Bu yaklaşım, yeniden yazma kuralları dosyalarınızın konumu için daha fazla esneklik sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-622">This approach may provide greater flexibility for the location of your rewrite rules files.</span></span> <span data-ttu-id="726e4-623">Yeniden yazma kuralları dosyalarınızın sağladığınız yolda sunucuya dağıtıldığınızdan emin olun.</span><span class="sxs-lookup"><span data-stu-id="726e4-623">Make sure that your rewrite rules files are deployed to the server at the path you provide.</span></span>
>
> ```csharp
> PhysicalFileProvider fileProvider = new PhysicalFileProvider(Directory.GetCurrentDirectory());
> ```

### <a name="method-based-rule"></a><span data-ttu-id="726e4-624">Yöntem tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="726e4-624">Method-based rule</span></span>

<span data-ttu-id="726e4-625">Bir <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> yöntemde kendi kural mantığınızı uygulamak için kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-625">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to implement your own rule logic in a method.</span></span> <span data-ttu-id="726e4-626">`Add`<xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, yönteminizde kullanıma hazır <xref:Microsoft.AspNetCore.Http.HttpContext> hale getirir.</span><span class="sxs-lookup"><span data-stu-id="726e4-626">`Add` exposes the <xref:Microsoft.AspNetCore.Rewrite.RewriteContext>, which makes available the <xref:Microsoft.AspNetCore.Http.HttpContext> for use in your method.</span></span> <span data-ttu-id="726e4-627">[Yeniden Yazma Bağlamı.Sonuç,](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) ek boru hattı işlemenin nasıl işleneceğini belirler.</span><span class="sxs-lookup"><span data-stu-id="726e4-627">The [RewriteContext.Result](xref:Microsoft.AspNetCore.Rewrite.RewriteContext.Result*) determines how additional pipeline processing is handled.</span></span> <span data-ttu-id="726e4-628">Değeri aşağıdaki tabloda <xref:Microsoft.AspNetCore.Rewrite.RuleResult> açıklanan alanlardan birine ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-628">Set the value to one of the <xref:Microsoft.AspNetCore.Rewrite.RuleResult> fields described in the following table.</span></span>

| `RewriteContext.Result`              | <span data-ttu-id="726e4-629">Eylem</span><span class="sxs-lookup"><span data-stu-id="726e4-629">Action</span></span>                                                           |
| ------------------------------------ | ---------------------------------------------------------------- |
| <span data-ttu-id="726e4-630">`RuleResult.ContinueRules`(varsayılan)</span><span class="sxs-lookup"><span data-stu-id="726e4-630">`RuleResult.ContinueRules` (default)</span></span> | <span data-ttu-id="726e4-631">Kuralları uygulamaya devam edin.</span><span class="sxs-lookup"><span data-stu-id="726e4-631">Continue applying rules.</span></span>                                         |
| `RuleResult.EndResponse`             | <span data-ttu-id="726e4-632">Kuralları uygulamayı bırakın ve yanıtı gönderin.</span><span class="sxs-lookup"><span data-stu-id="726e4-632">Stop applying rules and send the response.</span></span>                       |
| `RuleResult.SkipRemainingRules`      | <span data-ttu-id="726e4-633">Kuralları uygulamayı bırakın ve bağlamı bir sonraki ara yazılıma gönderin.</span><span class="sxs-lookup"><span data-stu-id="726e4-633">Stop applying rules and send the context to the next middleware.</span></span> |

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=14)]

<span data-ttu-id="726e4-634">Örnek *uygulama.xml*ile biten yollar için istekleri yeniden yönlendiren bir yöntem gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-634">The sample app demonstrates a method that redirects requests for paths that end with *.xml*.</span></span> <span data-ttu-id="726e4-635">Bir `/file.xml`istek için yapılırsa, istek `/xmlfiles/file.xml`.</span><span class="sxs-lookup"><span data-stu-id="726e4-635">If a request is made for `/file.xml`, the request is redirected to `/xmlfiles/file.xml`.</span></span> <span data-ttu-id="726e4-636">Durum kodu *301*olarak ayarlanır - Kalıcı olarak taşınır.</span><span class="sxs-lookup"><span data-stu-id="726e4-636">The status code is set to *301 - Moved Permanently*.</span></span> <span data-ttu-id="726e4-637">Tarayıcı */xmlfiles/file.xml*için yeni bir istekte bulunursa, Statik Dosya Middleware *wwwroot/xmlfiles* klasöründen dosyayı istemciye sunar.</span><span class="sxs-lookup"><span data-stu-id="726e4-637">When the browser makes a new request for */xmlfiles/file.xml*, Static File Middleware serves the file to the client from the *wwwroot/xmlfiles* folder.</span></span> <span data-ttu-id="726e4-638">Yeniden yönlendirme için yanıtın durum kodunu açıkça ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="726e4-638">For a redirect, explicitly set the status code of the response.</span></span> <span data-ttu-id="726e4-639">Aksi takdirde, *200 - Tamam* durum kodu döndürülür ve yönlendirme istemcide oluşmaz.</span><span class="sxs-lookup"><span data-stu-id="726e4-639">Otherwise, a *200 - OK* status code is returned, and the redirect doesn't occur on the client.</span></span>

<span data-ttu-id="726e4-640">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="726e4-640">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectXmlFileRequests&highlight=14-18)]

<span data-ttu-id="726e4-641">Bu yaklaşım istekleri yeniden yazabilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-641">This approach can also rewrite requests.</span></span> <span data-ttu-id="726e4-642">Örnek *uygulama, wwwroot* klasöründen *file.txt* metin dosyasına hizmet vermek için herhangi bir metin dosyası isteğinin yolunu yeniden yazmayı gösterir.</span><span class="sxs-lookup"><span data-stu-id="726e4-642">The sample app demonstrates rewriting the path for any text file request to serve the *file.txt* text file from the *wwwroot* folder.</span></span> <span data-ttu-id="726e4-643">Statik Dosya Middleware güncelleştirilmiş istek yolunu temel alarak dosyaya hizmet vermektedir:</span><span class="sxs-lookup"><span data-stu-id="726e4-643">Static File Middleware serves the file based on the updated request path:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=15,22)]

<span data-ttu-id="726e4-644">*RewriteRules.cs*:</span><span class="sxs-lookup"><span data-stu-id="726e4-644">*RewriteRules.cs*:</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RewriteTextFileRequests&highlight=7-8)]

### <a name="irule-based-rule"></a><span data-ttu-id="726e4-645">IRule tabanlı kural</span><span class="sxs-lookup"><span data-stu-id="726e4-645">IRule-based rule</span></span>

<span data-ttu-id="726e4-646">Arabirimi uygulayan bir sınıfta kural mantığını kullanmak için <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> <xref:Microsoft.AspNetCore.Rewrite.IRule> kullanın.</span><span class="sxs-lookup"><span data-stu-id="726e4-646">Use <xref:Microsoft.AspNetCore.Rewrite.RewriteOptionsExtensions.Add*> to use rule logic in a class that implements the <xref:Microsoft.AspNetCore.Rewrite.IRule> interface.</span></span> <span data-ttu-id="726e4-647">`IRule`yöntem tabanlı kural yaklaşımını kullanarak daha fazla esneklik sağlar.</span><span class="sxs-lookup"><span data-stu-id="726e4-647">`IRule` provides greater flexibility over using the method-based rule approach.</span></span> <span data-ttu-id="726e4-648">Uygulama sınıfınız, <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> yöntem için parametreleri geçirmenize olanak tanıyan bir oluşturucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-648">Your implementation class may include a constructor that allows you can pass in parameters for the <xref:Microsoft.AspNetCore.Rewrite.IRule.ApplyRule*> method.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/Startup.cs?name=snippet1&highlight=16-17)]

<span data-ttu-id="726e4-649">Örnek uygulamadaki parametrelerin değerleri için `extension` ve `newPath` çeşitli koşulları karşılamak için kontrol edilir.</span><span class="sxs-lookup"><span data-stu-id="726e4-649">The values of the parameters in the sample app for the `extension` and the `newPath` are checked to meet several conditions.</span></span> <span data-ttu-id="726e4-650">Bir `extension` değer içermelidir ve değeri *.png*, *.jpg*veya *.gif*olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="726e4-650">The `extension` must contain a value, and the value must be *.png*, *.jpg*, or *.gif*.</span></span> <span data-ttu-id="726e4-651">Geçerli `newPath` değilse, bir <xref:System.ArgumentException> atılır.</span><span class="sxs-lookup"><span data-stu-id="726e4-651">If the `newPath` isn't valid, an <xref:System.ArgumentException> is thrown.</span></span> <span data-ttu-id="726e4-652">*image.png*için bir istek yapılırsa, `/png-images/image.png`istek .</span><span class="sxs-lookup"><span data-stu-id="726e4-652">If a request is made for *image.png*, the request is redirected to `/png-images/image.png`.</span></span> <span data-ttu-id="726e4-653">*image.jpg*için bir istek yapılırsa, `/jpg-images/image.jpg`istek .</span><span class="sxs-lookup"><span data-stu-id="726e4-653">If a request is made for *image.jpg*, the request is redirected to `/jpg-images/image.jpg`.</span></span> <span data-ttu-id="726e4-654">Durum kodu *301*olarak ayarlanır - Kalıcı `context.Result` olarak taşınır ve işleme kurallarını durdurmak ve yanıtı göndermek için ayarlanır.</span><span class="sxs-lookup"><span data-stu-id="726e4-654">The status code is set to *301 - Moved Permanently*, and the `context.Result` is set to stop processing rules and send the response.</span></span>

[!code-csharp[](url-rewriting/samples/2.x/SampleApp/RewriteRules.cs?name=snippet_RedirectImageRequests)]

<span data-ttu-id="726e4-655">Orijinal İstek:`/image.png`</span><span class="sxs-lookup"><span data-stu-id="726e4-655">Original Request: `/image.png`</span></span>

![Geliştirici Araçları ile tarayıcı penceresi image.png için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_png_requests.png)

<span data-ttu-id="726e4-657">Orijinal İstek:`/image.jpg`</span><span class="sxs-lookup"><span data-stu-id="726e4-657">Original Request: `/image.jpg`</span></span>

![Geliştirici Araçları ile tarayıcı penceresi image.jpg için istek ve yanıtları izleme](url-rewriting/_static/add_redirect_jpg_requests.png)

## <a name="regex-examples"></a><span data-ttu-id="726e4-659">Regex örnekleri</span><span class="sxs-lookup"><span data-stu-id="726e4-659">Regex examples</span></span>

| <span data-ttu-id="726e4-660">Hedef</span><span class="sxs-lookup"><span data-stu-id="726e4-660">Goal</span></span> | <span data-ttu-id="726e4-661">Regex String &</span><span class="sxs-lookup"><span data-stu-id="726e4-661">Regex String &</span></span><br><span data-ttu-id="726e4-662">Eşle' nin Örneği</span><span class="sxs-lookup"><span data-stu-id="726e4-662">Match Example</span></span> | <span data-ttu-id="726e4-663">Yedek Dize &</span><span class="sxs-lookup"><span data-stu-id="726e4-663">Replacement String &</span></span><br><span data-ttu-id="726e4-664">Çıktı Örneği</span><span class="sxs-lookup"><span data-stu-id="726e4-664">Output Example</span></span> |
| ---- | ------------------------------- | -------------------------------------- |
| <span data-ttu-id="726e4-665">Yolu querystring'e yeniden yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-665">Rewrite path into querystring</span></span> | `^path/(.*)/(.*)`<br>`/path/abc/123` | `path?var1=$1&var2=$2`<br>`/path?var1=abc&var2=123` |
| <span data-ttu-id="726e4-666">Şerit izleme eğik çizgi</span><span class="sxs-lookup"><span data-stu-id="726e4-666">Strip trailing slash</span></span> | `(.*)/$`<br>`/path/` | `$1`<br>`/path` |
| <span data-ttu-id="726e4-667">Sondaki eğik çizgiyi zorla</span><span class="sxs-lookup"><span data-stu-id="726e4-667">Enforce trailing slash</span></span> | `(.*[^/])$`<br>`/path` | `$1/`<br>`/path/` |
| <span data-ttu-id="726e4-668">Belirli istekleri yeniden yazmaktan kaçının</span><span class="sxs-lookup"><span data-stu-id="726e4-668">Avoid rewriting specific requests</span></span> | <span data-ttu-id="726e4-669">`^(.*)(?<!\.axd)$` veya `^(?!.*\.axd$)(.*)$`</span><span class="sxs-lookup"><span data-stu-id="726e4-669">`^(.*)(?<!\.axd)$` or `^(?!.*\.axd$)(.*)$`</span></span><br><span data-ttu-id="726e4-670">Evet:`/resource.htm`</span><span class="sxs-lookup"><span data-stu-id="726e4-670">Yes: `/resource.htm`</span></span><br><span data-ttu-id="726e4-671">№:`/resource.axd`</span><span class="sxs-lookup"><span data-stu-id="726e4-671">No: `/resource.axd`</span></span> | `rewritten/$1`<br>`/rewritten/resource.htm`<br>`/resource.axd` |
| <span data-ttu-id="726e4-672">URL segmentlerini yeniden düzenleme</span><span class="sxs-lookup"><span data-stu-id="726e4-672">Rearrange URL segments</span></span> | `path/(.*)/(.*)/(.*)`<br>`path/1/2/3` | `path/$3/$2/$1`<br>`path/3/2/1` |
| <span data-ttu-id="726e4-673">URL kesimini değiştirme</span><span class="sxs-lookup"><span data-stu-id="726e4-673">Replace a URL segment</span></span> | `^(.*)/segment2/(.*)`<br>`/segment1/segment2/segment3` | `$1/replaced/$2`<br>`/segment1/replaced/segment3` |

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="726e4-674">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="726e4-674">Additional resources</span></span>

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* [<span data-ttu-id="726e4-675">.NET içinde normal ifadeler</span><span class="sxs-lookup"><span data-stu-id="726e4-675">Regular expressions in .NET</span></span>](/dotnet/articles/standard/base-types/regular-expressions)
* [<span data-ttu-id="726e4-676">Normal ifade dili - hızlı başvuru</span><span class="sxs-lookup"><span data-stu-id="726e4-676">Regular expression language - quick reference</span></span>](/dotnet/articles/standard/base-types/quick-ref)
* [<span data-ttu-id="726e4-677">Apaçi mod_rewrite</span><span class="sxs-lookup"><span data-stu-id="726e4-677">Apache mod_rewrite</span></span>](https://httpd.apache.org/docs/2.4/rewrite/)
* [<span data-ttu-id="726e4-678">Url Yeniden Yazma Modülü 2.0'ı kullanma (IIS için)</span><span class="sxs-lookup"><span data-stu-id="726e4-678">Using Url Rewrite Module 2.0 (for IIS)</span></span>](/iis/extensions/url-rewrite-module/using-url-rewrite-module-20)
* [<span data-ttu-id="726e4-679">URL Yeniden Yazma Modülü Yapılandırma Başvurusu</span><span class="sxs-lookup"><span data-stu-id="726e4-679">URL Rewrite Module Configuration Reference</span></span>](/iis/extensions/url-rewrite-module/url-rewrite-module-configuration-reference)
* [<span data-ttu-id="726e4-680">IIS URL Yeniden Modül Forumu</span><span class="sxs-lookup"><span data-stu-id="726e4-680">IIS URL Rewrite Module Forum</span></span>](https://forums.iis.net/1152.aspx)
* [<span data-ttu-id="726e4-681">Basit bir URL yapısını koruyun</span><span class="sxs-lookup"><span data-stu-id="726e4-681">Keep a simple URL structure</span></span>](https://support.google.com/webmasters/answer/76329?hl=en)
* [<span data-ttu-id="726e4-682">10 URL İpuçları ve Püf Noktaları Yeniden Yazma</span><span class="sxs-lookup"><span data-stu-id="726e4-682">10 URL Rewriting Tips and Tricks</span></span>](https://ruslany.net/2009/04/10-url-rewriting-tips-and-tricks/)
* [<span data-ttu-id="726e4-683">Kesmek ya da kesmemek için</span><span class="sxs-lookup"><span data-stu-id="726e4-683">To slash or not to slash</span></span>](https://webmasters.googleblog.com/2010/04/to-slash-or-not-to-slash.html)
