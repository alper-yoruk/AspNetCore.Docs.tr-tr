---
title: Yanıt verilerini ASP.NET Çekirdek Web API'sinde biçimlendirme
author: ardalis
description: Yanıt verilerini ASP.NET Core Web API'de nasıl biçimlendireceklerini öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
uid: web-api/advanced/formatting
ms.openlocfilehash: 392e4905126ffb6801cc55055f1d511f5fa99dd1
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642705"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="ec52a-103">Yanıt verilerini ASP.NET Çekirdek Web API'sinde biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="ec52a-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="ec52a-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ec52a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ec52a-105">ASP.NET Core MVC yanıt verilerini biçimlendirme desteğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="ec52a-106">Yanıt verileri belirli biçimler kullanılarak veya istemcinin istediği biçime yanıt olarak biçimlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="ec52a-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ec52a-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="ec52a-108">Biçime Özel Eylem Sonuçları</span><span class="sxs-lookup"><span data-stu-id="ec52a-108">Format-specific Action Results</span></span>

<span data-ttu-id="ec52a-109">Bazı eylem sonuç türleri belirli bir biçime özgü, gibi <xref:Microsoft.AspNetCore.Mvc.JsonResult> ve <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span><span class="sxs-lookup"><span data-stu-id="ec52a-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="ec52a-110">Eylemler, istemci tercihlerinden bağımsız olarak belirli bir biçimde biçimlendirilmiş sonuçlar döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="ec52a-111">Örneğin, json `JsonResult` biçimlendirilmiş verileri döndüren döndürür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="ec52a-112">İade `ContentResult` veya dize düz metin biçimlendirilmiş dize verilerini döndürür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="ec52a-113">Belirli bir türü döndürmek için bir eylem gerekmez.</span><span class="sxs-lookup"><span data-stu-id="ec52a-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="ec52a-114">ASP.NET Core herhangi bir nesne iade değerini destekler.</span><span class="sxs-lookup"><span data-stu-id="ec52a-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="ec52a-115">Türü olmayan <xref:Microsoft.AspNetCore.Mvc.IActionResult> nesneleri döndüren eylemlerin sonuçları, uygun <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> uygulama kullanılarak seri olarak işlenir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="ec52a-116">Daha fazla bilgi için bkz. <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="ec52a-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="ec52a-117">Yerleşik yardımcı yöntemi <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> JSON biçimlendirilmiş verileri döndürür:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="ec52a-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="ec52a-118">Örnek karşıdan yükleme, yazar listesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="ec52a-119">Önceki kod ile F12 tarayıcı geliştirici araçları veya [Postacı](https://www.getpostman.com/tools) kullanma:</span><span class="sxs-lookup"><span data-stu-id="ec52a-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="ec52a-120">**İçerik türünü** `application/json; charset=utf-8` içeren yanıt üstbilgi: görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="ec52a-121">İstek üstbilgisi görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-121">The request headers are displayed.</span></span> <span data-ttu-id="ec52a-122">Örneğin, `Accept` üstbilgi.</span><span class="sxs-lookup"><span data-stu-id="ec52a-122">For example, the `Accept` header.</span></span> <span data-ttu-id="ec52a-123">Üstbilgi `Accept` önceki kod tarafından yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="ec52a-124">Düz metin biçimlendirilmiş verileri <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> döndürmek <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> için, kullanımı ve yardımcısı:</span><span class="sxs-lookup"><span data-stu-id="ec52a-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="ec52a-125">Önceki kodda, `Content-Type` `text/plain`döndürülen.</span><span class="sxs-lookup"><span data-stu-id="ec52a-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="ec52a-126">Bir dize `Content-Type` döndürme `text/plain`sunar:</span><span class="sxs-lookup"><span data-stu-id="ec52a-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="ec52a-127">Birden çok iade türüne `IActionResult`sahip eylemler için return.</span><span class="sxs-lookup"><span data-stu-id="ec52a-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="ec52a-128">Örneğin, gerçekleştirilen işlemlerin sonucuna göre farklı HTTP durum kodları döndürme.</span><span class="sxs-lookup"><span data-stu-id="ec52a-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="ec52a-129">İçerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="ec52a-129">Content negotiation</span></span>

<span data-ttu-id="ec52a-130">İçerik anlaşması, istemci bir Kabul [üstbilgisini](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)belirttiğinde oluşur.</span><span class="sxs-lookup"><span data-stu-id="ec52a-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="ec52a-131">ASP.NET Core tarafından kullanılan varsayılan biçim [JSON'dur.](https://json.org/)</span><span class="sxs-lookup"><span data-stu-id="ec52a-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="ec52a-132">İçerik pazarlığı:</span><span class="sxs-lookup"><span data-stu-id="ec52a-132">Content negotiation is:</span></span>

* <span data-ttu-id="ec52a-133">tarafından <xref:Microsoft.AspNetCore.Mvc.ObjectResult>uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="ec52a-134">Yardımcı yöntemlerden döndürülen durum koduna özgü eylem sonuçları yerleşiktir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="ec52a-135">Eylem sonuçları yardımcı yöntemleri dayanır. `ObjectResult`</span><span class="sxs-lookup"><span data-stu-id="ec52a-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="ec52a-136">Bir model türü döndürüldüğünde, iade `ObjectResult`türü .</span><span class="sxs-lookup"><span data-stu-id="ec52a-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="ec52a-137">Aşağıdaki eylem yöntemi `Ok` ve `NotFound` yardımcı yöntemleri kullanır:</span><span class="sxs-lookup"><span data-stu-id="ec52a-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="ec52a-138">Varsayılan olarak, ASP.NET `application/json` `text/json`Core `text/plain` destekler , ve medya türleri.</span><span class="sxs-lookup"><span data-stu-id="ec52a-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="ec52a-139">[Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/tools) gibi araçlar, iade biçimini belirtmek için istek üstbilgisini `Accept` ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="ec52a-140">`Accept` Üstbilgi sunucunun desteklediği bir tür içeriyorsa, bu tür döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="ec52a-141">Sonraki bölümde ek formatters nasıl ekleyeceğinigösterir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="ec52a-142">Denetleyici eylemleri POCOs (Düz Eski CLR Nesneleri) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="ec52a-143">Bir POCO döndürüldüğünde, çalışma zamanı nesneyi `ObjectResult` saran bir otomatik olarak oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ec52a-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="ec52a-144">İstemci biçimlendirilmiş serileştirilmiş nesneyi alır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="ec52a-145">Döndürülen nesne ise, `null` `204 No Content` yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="ec52a-146">Nesne türünü döndürme:</span><span class="sxs-lookup"><span data-stu-id="ec52a-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="ec52a-147">Önceki kodda, geçerli bir yazar takma adı `200 OK` için bir istek yazarın verileriyle bir yanıt döndürür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="ec52a-148">Geçersiz bir takma ad isteği `204 No Content` yanıtı döndürür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="ec52a-149">Kabul başlığı</span><span class="sxs-lookup"><span data-stu-id="ec52a-149">The Accept header</span></span>

<span data-ttu-id="ec52a-150">İçerik *anlaşması,* istekte bir `Accept` üstbilgi göründüğünde gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="ec52a-151">Bir istek kabul üstbilgisi içeriyorsa, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ec52a-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="ec52a-152">Tercih sırasına göre kabul üstbilgisinde ortam türlerini sıralar.</span><span class="sxs-lookup"><span data-stu-id="ec52a-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="ec52a-153">Belirtilen biçimlerden birinde yanıt üretebilecek bir madde bulmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="ec52a-154">Müşterinin isteğini karşılayabilen herhangi bir madde bulunmazsa, ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ec52a-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="ec52a-155">Ayarlanmışsa `406 Not Acceptable` <xref:Microsoft.AspNetCore.Mvc.MvcOptions> döndürür veya -</span><span class="sxs-lookup"><span data-stu-id="ec52a-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="ec52a-156">Yanıt üretebilecek ilk formatter'ı bulmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="ec52a-157">İstenen biçim için hiçbir formatter yapılandırılmamışsa, nesneyi biçimlendirebilen ilk formatter kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="ec52a-158">İstekte `Accept` üstbilgi görünmüyorsa:</span><span class="sxs-lookup"><span data-stu-id="ec52a-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="ec52a-159">Nesneyi işleyebilir ilk formatter yanıtı serileştirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="ec52a-160">Herhangi bir pazarlık falan yok.</span><span class="sxs-lookup"><span data-stu-id="ec52a-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="ec52a-161">Sunucu hangi biçimin döndürülecek olduğunu belirliyor.</span><span class="sxs-lookup"><span data-stu-id="ec52a-161">The server is determining what format to return.</span></span>

<span data-ttu-id="ec52a-162">Üstbilgi kabul içeriyorsa, `*/*`üstbilgi `RespectBrowserAcceptHeader` üzerinde doğru ayarlanmadığı sürece <xref:Microsoft.AspNetCore.Mvc.MvcOptions>yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="ec52a-163">Tarayıcılar ve içerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="ec52a-163">Browsers and content negotiation</span></span>

<span data-ttu-id="ec52a-164">Tipik API istemcilerinin aksine, `Accept` web tarayıcıları üstbilgi sağlar.</span><span class="sxs-lookup"><span data-stu-id="ec52a-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="ec52a-165">Web tarayıcısı joker karakterler de dahil olmak üzere birçok biçimi belirtir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="ec52a-166">Varsayılan olarak, çerçeve isteğibir tarayıcıdan geldiğini algıladığında:</span><span class="sxs-lookup"><span data-stu-id="ec52a-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="ec52a-167">Üstbilgi `Accept` yoksayılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="ec52a-168">İçerik, aksi şekilde yapılandırılmadığı sürece JSON'da döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="ec52a-169">Bu, API'leri tüketirken tarayıcılar arasında daha tutarlı bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ec52a-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="ec52a-170">Tarayıcı kabul üstbilgi onurlandırmak için bir <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> uygulama `true`yapılandırmak için, ayarlayın:</span><span class="sxs-lookup"><span data-stu-id="ec52a-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="ec52a-171">Formatters yapılandırılması</span><span class="sxs-lookup"><span data-stu-id="ec52a-171">Configure formatters</span></span>

<span data-ttu-id="ec52a-172">Ek biçimleri desteklemesi gereken uygulamalar uygun NuGet paketlerini ekleyebilir ve desteği yapılandırabilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="ec52a-173">Giriş ve çıkış için ayrı konular vardır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="ec52a-174">Giriş formatters Model [Bağlama](xref:mvc/models/model-binding)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ec52a-175">Çıktı formatters yanıtları biçimlendirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="ec52a-176">Özel bir formatter oluşturma hakkında bilgi için, [Bkz. Özel Formatters.](xref:web-api/advanced/custom-formatters)</span><span class="sxs-lookup"><span data-stu-id="ec52a-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="ec52a-177">XML biçim desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="ec52a-177">Add XML format support</span></span>

<span data-ttu-id="ec52a-178">XML formatters kullanılarak <xref:System.Xml.Serialization.XmlSerializer> uygulanan çağrı <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>ile yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="ec52a-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="ec52a-179">Önceki kod kullanarak `XmlSerializer`sonuçları serileştirir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="ec52a-180">Önceki kodu kullanırken, denetleyici yöntemleri isteğin `Accept` üstbilgisini temel alan uygun biçimi döndürer.</span><span class="sxs-lookup"><span data-stu-id="ec52a-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="ec52a-181">Configure System.Text.Json tabanlı formatters</span><span class="sxs-lookup"><span data-stu-id="ec52a-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="ec52a-182">`System.Text.Json`-tabanlı formatters için özellikler kullanılarak `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="ec52a-183">Çıkış serileştirme seçenekleri, eylem başına bazda, kullanılarak `JsonResult`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="ec52a-184">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ec52a-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="ec52a-185">Newtonsoft.Json tabanlı JSON format desteği ekle</span><span class="sxs-lookup"><span data-stu-id="ec52a-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="ec52a-186">Core 3.0'ı ASP.NET önce, varsayılan olarak `Newtonsoft.Json` paket kullanılarak uygulanan JSON formatters kullanılmıştır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="ec52a-187">ASP.NET Core 3.0 veya sonraki olarak, varsayılan JSON formatters `System.Text.Json`dayanmaktadır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="ec52a-188">[Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketini yükleyerek ve .'de `Startup.ConfigureServices`yapılandırarak temel formatters ve özellikler için `Newtonsoft.Json` destek mevcuttur.</span><span class="sxs-lookup"><span data-stu-id="ec52a-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="ec52a-189">Bazı özellikler -tabanlı `System.Text.Json`formatters ile iyi çalışmayabilir `Newtonsoft.Json`ve -tabanlı formatters bir referans gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="ec52a-190">Uygulama aşağıdaki `Newtonsoft.Json`gibi esasını kullanmaya devam edin:</span><span class="sxs-lookup"><span data-stu-id="ec52a-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="ec52a-191">Öznitelikleri kullanır. `Newtonsoft.Json`</span><span class="sxs-lookup"><span data-stu-id="ec52a-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="ec52a-192">Örneğin `[JsonProperty]` veya `[JsonIgnore]` olabilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="ec52a-193">Serileştirme ayarlarını özelleştirin.</span><span class="sxs-lookup"><span data-stu-id="ec52a-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="ec52a-194">`Newtonsoft.Json` Sağlayan özelliklere dayanır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="ec52a-195">Yapılandırır. `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`</span><span class="sxs-lookup"><span data-stu-id="ec52a-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="ec52a-196">Core 3.0ASP.NETden `JsonResult.SerializerSettings` önce, `Newtonsoft.Json`'ye `JsonSerializerSettings` özgü bir örneği kabul eder.</span><span class="sxs-lookup"><span data-stu-id="ec52a-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="ec52a-197">[OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) belgeleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ec52a-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="ec52a-198">`Newtonsoft.Json`-tabanlı formatters için özellikler kullanılarak `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="ec52a-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="ec52a-199">Çıkış serileştirme seçenekleri, eylem başına bazda, kullanılarak `JsonResult`yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="ec52a-200">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ec52a-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        options.Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="ec52a-201">XML biçim desteği ekleme</span><span class="sxs-lookup"><span data-stu-id="ec52a-201">Add XML format support</span></span>

<span data-ttu-id="ec52a-202">XML biçimlendirme [microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet paketi gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="ec52a-203">XML formatters kullanılarak <xref:System.Xml.Serialization.XmlSerializer> uygulanan çağrı <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>ile yapılandırılır:</span><span class="sxs-lookup"><span data-stu-id="ec52a-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="ec52a-204">Önceki kod kullanarak `XmlSerializer`sonuçları serileştirir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="ec52a-205">Önceki kodu kullanırken, denetleyici yöntemleri isteğin `Accept` üstbilgisini temel alan uygun biçimi döndürmelidir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="ec52a-206">Biçim belirtin</span><span class="sxs-lookup"><span data-stu-id="ec52a-206">Specify a format</span></span>

<span data-ttu-id="ec52a-207">Yanıt biçimlerini kısıtlamak için [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtreyi uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ec52a-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="ec52a-208">Çoğu [Filtre](xref:mvc/controllers/filters)gibi, `[Produces]` eylem, denetleyici veya genel kapsamda uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ec52a-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="ec52a-209">Önceki [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre:</span><span class="sxs-lookup"><span data-stu-id="ec52a-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="ec52a-210">Denetleyici içindeki tüm eylemleri JSON biçimlendirilmiş yanıtları döndürmeye zorlar.</span><span class="sxs-lookup"><span data-stu-id="ec52a-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="ec52a-211">Diğer formatters yapılandırılır ve istemci farklı bir biçim belirtir, JSON döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="ec52a-212">Daha fazla bilgi için [Bkz. Filtreler.](xref:mvc/controllers/filters)</span><span class="sxs-lookup"><span data-stu-id="ec52a-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="ec52a-213">Özel durum formatters</span><span class="sxs-lookup"><span data-stu-id="ec52a-213">Special case formatters</span></span>

<span data-ttu-id="ec52a-214">Bazı özel durumlar yerleşik formatters kullanılarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="ec52a-215">Varsayılan `string` olarak, iade türleri *metin/düz* `Accept` (üstbilgi üzerinden istenirse*metin/html)* olarak biçimlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="ec52a-216">Bu davranış kaldırArak <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>silinebilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="ec52a-217">`ConfigureServices` Formatters yöntemde kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="ec52a-218">Döndürerken model nesnesi `204 No Content` döndürme türü ne zaman döndürülen `null`eylemler.</span><span class="sxs-lookup"><span data-stu-id="ec52a-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="ec52a-219">Bu davranış kaldırArak <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>silinebilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="ec52a-220">Aşağıdaki kod kaldırır `StringOutputFormatter` ve `HttpNoContentOutputFormatter`.</span><span class="sxs-lookup"><span data-stu-id="ec52a-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="ec52a-221">Olmadan `StringOutputFormatter`, dahili JSON formatter `string` biçimleri dönüş türleri.</span><span class="sxs-lookup"><span data-stu-id="ec52a-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="ec52a-222">Yerleşik JSON formatter kaldırılır ve bir XML formatter varsa, XML formatter biçimleri `string` döndürme türleri.</span><span class="sxs-lookup"><span data-stu-id="ec52a-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="ec52a-223">Aksi `string` takdirde, `406 Not Acceptable`iade türleri döndürün.</span><span class="sxs-lookup"><span data-stu-id="ec52a-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="ec52a-224">Olmadan `HttpNoContentOutputFormatter`, null nesneleri yapılandırılmış formatter kullanılarak biçimlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="ec52a-225">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ec52a-225">For example:</span></span>

* <span data-ttu-id="ec52a-226">JSON formatter bir vücut ile `null`bir yanıt döndürür .</span><span class="sxs-lookup"><span data-stu-id="ec52a-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="ec52a-227">XML formatter öznitelik `xsi:nil="true"` kümesi ile boş bir XML öğesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="ec52a-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="ec52a-228">Yanıt biçimi URL eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="ec52a-228">Response format URL mappings</span></span>

<span data-ttu-id="ec52a-229">İstemciler URL'nin bir parçası olarak belirli bir biçim isteyebilirler, örneğin:</span><span class="sxs-lookup"><span data-stu-id="ec52a-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="ec52a-230">Sorgu dizesinde veya yolun bir kısmında.</span><span class="sxs-lookup"><span data-stu-id="ec52a-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="ec52a-231">.xml veya .json gibi biçime özgü bir dosya uzantısı kullanarak.</span><span class="sxs-lookup"><span data-stu-id="ec52a-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="ec52a-232">İstek yolundan eşleme, API'nin kullandığı rotada belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ec52a-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="ec52a-233">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="ec52a-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="ec52a-234">Önceki rota, istenen biçimin isteğe bağlı bir dosya uzantısı olarak belirtilmesine olanak tanır.</span><span class="sxs-lookup"><span data-stu-id="ec52a-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="ec52a-235">Öznitelik, [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) biçim değerinin varlığını denetler `RouteData` ve yanıt oluşturulduğunda yanıt biçimini uygun maddeyle eşler.</span><span class="sxs-lookup"><span data-stu-id="ec52a-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="ec52a-236">Yol</span><span class="sxs-lookup"><span data-stu-id="ec52a-236">Route</span></span>        |             <span data-ttu-id="ec52a-237">Biçimlendiricisi</span><span class="sxs-lookup"><span data-stu-id="ec52a-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="ec52a-238">Varsayılan çıkış formatter</span><span class="sxs-lookup"><span data-stu-id="ec52a-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="ec52a-239">JSON formatter (yapılandırılırsa)</span><span class="sxs-lookup"><span data-stu-id="ec52a-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="ec52a-240">XML formatter (yapılandırılırsa)</span><span class="sxs-lookup"><span data-stu-id="ec52a-240">The XML formatter (if configured)</span></span>  |
