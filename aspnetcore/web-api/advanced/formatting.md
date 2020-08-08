---
title: ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirme
author: ardalis
description: ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirmeyi öğrenin.
ms.author: riande
ms.custom: H1Hack27Feb2017
ms.date: 04/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/advanced/formatting
ms.openlocfilehash: 8aa94bd1f33d1dd8ce8e7f50468ed60b4ccb2515
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019943"
---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="04481-103">ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="04481-103">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="04481-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="04481-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="04481-105">ASP.NET Core MVC, yanıt verilerini biçimlendirme desteğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="04481-105">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="04481-106">Yanıt verileri, belirli biçimler kullanılarak veya istemci tarafından istenen biçime yanıt olarak biçimlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="04481-106">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="04481-107">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="04481-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="04481-108">Formata özgü eylem sonuçları</span><span class="sxs-lookup"><span data-stu-id="04481-108">Format-specific Action Results</span></span>

<span data-ttu-id="04481-109">Bazı eylem sonuç türleri, ve gibi belirli bir biçime özgüdür <xref:Microsoft.AspNetCore.Mvc.JsonResult> <xref:Microsoft.AspNetCore.Mvc.ContentResult> .</span><span class="sxs-lookup"><span data-stu-id="04481-109">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="04481-110">Eylemler, istemci tercihlerinden bağımsız olarak belirli bir biçimde biçimlendirilen sonuçları döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="04481-110">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="04481-111">Örneğin, döndürme `JsonResult` JSON biçimli verileri döndürür.</span><span class="sxs-lookup"><span data-stu-id="04481-111">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="04481-112">Döndürme `ContentResult` veya dize, düz metin biçimli dize verileri döndürür.</span><span class="sxs-lookup"><span data-stu-id="04481-112">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="04481-113">Belirli bir tür döndürmek için bir eylem gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="04481-113">An action isn't required to return any specific type.</span></span> <span data-ttu-id="04481-114">ASP.NET Core, tüm nesne dönüş değerlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="04481-114">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="04481-115">Tür olmayan nesneleri döndüren eylemlerin sonuçları <xref:Microsoft.AspNetCore.Mvc.IActionResult> , uygun uygulama kullanılarak serileştirilir <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="04481-115">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="04481-116">Daha fazla bilgi için bkz. <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="04481-116">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="04481-117">Yerleşik yardımcı yöntemi <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> JSON biçimli verileri döndürür:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="04481-117">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="04481-118">Örnek indirme, yazarların listesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="04481-118">The sample download returns the list of authors.</span></span> <span data-ttu-id="04481-119">Önceki kodla F12 tarayıcı geliştirici araçları veya [Postman](https://www.getpostman.com/tools) kullanma:</span><span class="sxs-lookup"><span data-stu-id="04481-119">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="04481-120">**Content-Type:** içeren yanıt üst bilgisi `application/json; charset=utf-8` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="04481-120">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="04481-121">İstek üst bilgileri görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="04481-121">The request headers are displayed.</span></span> <span data-ttu-id="04481-122">Örneğin, `Accept` üst bilgi.</span><span class="sxs-lookup"><span data-stu-id="04481-122">For example, the `Accept` header.</span></span> <span data-ttu-id="04481-123">`Accept`Üst bilgi, önceki kod tarafından yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="04481-123">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="04481-124">Düz metin biçimli verileri döndürmek için <xref:Microsoft.AspNetCore.Mvc.ContentResult> ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> yardımcısını kullanın:</span><span class="sxs-lookup"><span data-stu-id="04481-124">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult> and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Content%2A> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="04481-125">Yukarıdaki kodda, `Content-Type` döndürülen `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="04481-125">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="04481-126">Şu şekilde bir dize `Content-Type` döndürür `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="04481-126">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="04481-127">Birden çok dönüş türüne sahip eylemler için, döndürün `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="04481-127">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="04481-128">Örneğin, gerçekleştirilen işlemlerin sonucuna göre farklı HTTP durum kodları döndürülüyor.</span><span class="sxs-lookup"><span data-stu-id="04481-128">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="04481-129">İçerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="04481-129">Content negotiation</span></span>

<span data-ttu-id="04481-130">İstemci bir [Accept üst bilgisi](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)belirttiğinde içerik anlaşması oluşur.</span><span class="sxs-lookup"><span data-stu-id="04481-130">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="04481-131">ASP.NET Core tarafından kullanılan varsayılan biçim [JSON](https://json.org/)'dir.</span><span class="sxs-lookup"><span data-stu-id="04481-131">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="04481-132">İçerik anlaşması:</span><span class="sxs-lookup"><span data-stu-id="04481-132">Content negotiation is:</span></span>

* <span data-ttu-id="04481-133">Uygulayan <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="04481-133">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="04481-134">Yardımcı metotlarından döndürülen durum koduna özgü eylem sonuçlarına yerleşik olarak.</span><span class="sxs-lookup"><span data-stu-id="04481-134">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="04481-135">Eylem sonuçları yardımcı yöntemleri temel alınır `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="04481-135">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="04481-136">Bir model türü döndürüldüğünde, dönüş türü olur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="04481-136">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="04481-137">Aşağıdaki eylem yöntemi `Ok` ve `NotFound` yardımcı yöntemlerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="04481-137">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="04481-138">Varsayılan olarak, ASP.NET Core `application/json` , `text/json` ve `text/plain` medya türlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="04481-138">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="04481-139">[Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/tools) gibi araçlar, `Accept` istek üst bilgisini dönüş biçimini belirtecek şekilde ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="04481-139">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="04481-140">`Accept`Üst bilgi, sunucunun desteklediği bir tür içerdiğinde, bu tür döndürülür.</span><span class="sxs-lookup"><span data-stu-id="04481-140">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="04481-141">Sonraki bölümde, ek Biçimlendiriciler ekleme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="04481-141">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="04481-142">Denetleyici eylemleri POCOs (düz eski CLR nesneleri) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="04481-142">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="04481-143">POCO döndürüldüğünde, çalışma zamanı nesneyi sarmalayan bir otomatik olarak oluşturur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="04481-143">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="04481-144">İstemci, biçimlendirilen serileştirilmiş nesneyi alır.</span><span class="sxs-lookup"><span data-stu-id="04481-144">The client gets the formatted serialized object.</span></span> <span data-ttu-id="04481-145">Döndürülen nesne ise `null` `204 No Content` yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="04481-145">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="04481-146">Nesne türü döndürülüyor:</span><span class="sxs-lookup"><span data-stu-id="04481-146">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="04481-147">Önceki kodda, geçerli bir yazar diğer adı için bir istek `200 OK` yazarın verileriyle bir yanıt döndürür.</span><span class="sxs-lookup"><span data-stu-id="04481-147">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="04481-148">Geçersiz bir diğer ad isteği bir yanıt döndürüyor `204 No Content` .</span><span class="sxs-lookup"><span data-stu-id="04481-148">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="04481-149">Accept üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="04481-149">The Accept header</span></span>

<span data-ttu-id="04481-150">İstekte *negotiation* bir `Accept` üst bilgi göründüğünde içerik anlaşması gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="04481-150">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="04481-151">Bir istek bir Accept üst bilgisi içerdiğinde ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="04481-151">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="04481-152">Kabul üst bilgisindeki medya türlerini tercih sırasına göre numaralandırır.</span><span class="sxs-lookup"><span data-stu-id="04481-152">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="04481-153">Belirtilen biçimlerden birinde yanıt üretemeyen bir biçimlendirici bulmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="04481-153">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="04481-154">İstemcinin isteğini karşılayabilen bir biçimlendirici bulunmazsa ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="04481-154">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="04481-155">`406 Not Acceptable`Ayarlanmışsa döndürür <xref:Microsoft.AspNetCore.Mvc.MvcOptions> veya-</span><span class="sxs-lookup"><span data-stu-id="04481-155">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="04481-156">Yanıt üreten ilk biçimlendirici bulmayı dener.</span><span class="sxs-lookup"><span data-stu-id="04481-156">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="04481-157">İstenen biçim için bir biçimlendirici yapılandırılmamışsa, nesneyi biçimlendirebileceğini ilk biçimlendirici kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04481-157">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="04481-158">İstekte hiçbir `Accept` başlık görünürse:</span><span class="sxs-lookup"><span data-stu-id="04481-158">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="04481-159">Nesneyi işleyebilen ilk biçimlendirici, yanıtı seri hale getirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04481-159">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="04481-160">Hiçbir anlaşma gerçekleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="04481-160">There isn't any negotiation taking place.</span></span> <span data-ttu-id="04481-161">Sunucu hangi biçimin döneceğine karar verir.</span><span class="sxs-lookup"><span data-stu-id="04481-161">The server is determining what format to return.</span></span>

<span data-ttu-id="04481-162">Accept üst bilgisi içeriyorsa `*/*` üstbilgi, `RespectBrowserAcceptHeader` true olarak ayarlanmadığı müddetçe yok sayılır <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="04481-162">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="04481-163">Tarayıcılar ve içerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="04481-163">Browsers and content negotiation</span></span>

<span data-ttu-id="04481-164">Tipik API istemcilerinin aksine, Web tarayıcıları `Accept` üst bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="04481-164">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="04481-165">Web tarayıcısı, joker karakterler dahil olmak üzere birçok biçim belirtir.</span><span class="sxs-lookup"><span data-stu-id="04481-165">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="04481-166">Varsayılan olarak, Framework isteğin bir tarayıcıdan geldiğini algıladığında:</span><span class="sxs-lookup"><span data-stu-id="04481-166">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="04481-167">`Accept`Üst bilgi yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="04481-167">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="04481-168">Aksi yapılandırılmadığı takdirde içerik JSON içinde döndürülür.</span><span class="sxs-lookup"><span data-stu-id="04481-168">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="04481-169">Bu, API 'Leri tükettiren tarayıcılarda daha tutarlı bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="04481-169">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="04481-170">Bir uygulamayı tarayıcı onay üstbilgilerini kabul edecek şekilde yapılandırmak için, şu <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="04481-170">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="04481-171">Biçimleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="04481-171">Configure formatters</span></span>

<span data-ttu-id="04481-172">Ek biçimleri desteklemesi gereken uygulamalar uygun NuGet paketlerini ekleyebilir ve desteği yapılandırabilir.</span><span class="sxs-lookup"><span data-stu-id="04481-172">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="04481-173">Giriş ve çıkış için ayrı biçimlendirme vardır.</span><span class="sxs-lookup"><span data-stu-id="04481-173">There are separate formatters for input and output.</span></span> <span data-ttu-id="04481-174">Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04481-174">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="04481-175">Çıkış biçimleri, yanıtları biçimlendirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="04481-175">Output formatters are used to format responses.</span></span> <span data-ttu-id="04481-176">Özel bir biçimlendirici oluşturma hakkında daha fazla bilgi için bkz. [özel Formatlayıcılar](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="04481-176">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="04481-177">XML biçimi desteği ekle</span><span class="sxs-lookup"><span data-stu-id="04481-177">Add XML format support</span></span>

<span data-ttu-id="04481-178">Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="04481-178">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="04481-179">Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="04481-179">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="04481-180">Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürür `Accept` .</span><span class="sxs-lookup"><span data-stu-id="04481-180">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="04481-181">System.Text.Jstabanlı formatlayıcıları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="04481-181">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="04481-182">`System.Text.Json`Tabanlı formatlayıcılar için özellikler kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="04481-182">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="04481-183">İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="04481-183">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="04481-184">Örnek:</span><span class="sxs-lookup"><span data-stu-id="04481-184">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="04481-185">Newtonsoft.Jstabanlı JSON biçimi desteği ekleyin</span><span class="sxs-lookup"><span data-stu-id="04481-185">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="04481-186">ASP.NET Core 3,0 ' dan önce, varsayılan olarak kullanılan JSON formatlayıcıları paket kullanılarak uygulanır `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="04481-186">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="04481-187">ASP.NET Core 3,0 veya sonraki bir sürümde, varsayılan JSON formatlayıcıları temel alınır `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="04481-187">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="04481-188">`Newtonsoft.Json`Temel biçimlendirme ve özellikler için destek, [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketi yüklenerek ve içinde yapılandırılarak kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="04481-188">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="04481-189">Bazı özellikler `System.Text.Json` , tabanlı formatlayıcılar ile düzgün çalışmayabilir ve `Newtonsoft.Json` tabanlı formatlara bir başvuru gerektirir.</span><span class="sxs-lookup"><span data-stu-id="04481-189">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="04481-190">`Newtonsoft.Json`Uygulama şu durumlarda tabanlı formatlayıcıları kullanmaya devam edin:</span><span class="sxs-lookup"><span data-stu-id="04481-190">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="04481-191">`Newtonsoft.Json`Öznitelikleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="04481-191">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="04481-192">Örneğin `[JsonProperty]` veya `[JsonIgnore]` olabilir.</span><span class="sxs-lookup"><span data-stu-id="04481-192">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="04481-193">Serileştirme ayarlarını özelleştirir.</span><span class="sxs-lookup"><span data-stu-id="04481-193">Customizes the serialization settings.</span></span>
* <span data-ttu-id="04481-194">, Tarafından sağlanan özellikleri kullanır `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="04481-194">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="04481-195">Yapılandırır `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` .</span><span class="sxs-lookup"><span data-stu-id="04481-195">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="04481-196">ASP.NET Core 3,0 ' dan önce, `JsonResult.SerializerSettings` öğesine özgü bir örneğini kabul eder `JsonSerializerSettings` `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="04481-196">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="04481-197">[Openapı](<xref:tutorials/web-api-help-pages-using-swagger>) belgeleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="04481-197">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="04481-198">`Newtonsoft.Json`Tabanlı formatlayıcılar için özellikler şu kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="04481-198">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="04481-199">İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="04481-199">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="04481-200">Örnek:</span><span class="sxs-lookup"><span data-stu-id="04481-200">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerSettings
    {
        Formatting = Formatting.Indented,
    });
}
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

### <a name="add-xml-format-support"></a><span data-ttu-id="04481-201">XML biçimi desteği ekle</span><span class="sxs-lookup"><span data-stu-id="04481-201">Add XML format support</span></span>

<span data-ttu-id="04481-202">XML biçimlendirme [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="04481-202">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="04481-203">Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="04481-203">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="04481-204">Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="04481-204">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="04481-205">Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürmelidir `Accept` .</span><span class="sxs-lookup"><span data-stu-id="04481-205">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="04481-206">Biçim belirtin</span><span class="sxs-lookup"><span data-stu-id="04481-206">Specify a format</span></span>

<span data-ttu-id="04481-207">Yanıt biçimlerini kısıtlamak için [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtreyi uygulayın.</span><span class="sxs-lookup"><span data-stu-id="04481-207">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="04481-208">Çoğu [filtre](xref:mvc/controllers/filters)gibi `[Produces]` eylem, denetleyici veya genel kapsamda de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="04481-208">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="04481-209">Önceki [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre:</span><span class="sxs-lookup"><span data-stu-id="04481-209">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="04481-210">Denetleyici içindeki tüm eylemleri JSON biçimli yanıtları döndürecek şekilde zorlar.</span><span class="sxs-lookup"><span data-stu-id="04481-210">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="04481-211">Diğer formatlayıcılar yapılandırıldıysa ve istemci farklı bir biçim belirtiyorsa JSON döndürülür.</span><span class="sxs-lookup"><span data-stu-id="04481-211">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="04481-212">Daha fazla bilgi için bkz. [Filtreler](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="04481-212">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="04481-213">Özel durum formatları</span><span class="sxs-lookup"><span data-stu-id="04481-213">Special case formatters</span></span>

<span data-ttu-id="04481-214">Bazı özel durumlar, yerleşik formatlayıcılar kullanılarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="04481-214">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="04481-215">Varsayılan olarak, `string` dönüş türleri *metin/düz* olarak biçimlendirilir (üst bilgi ile isteniyorsa*metin/html* `Accept` ).</span><span class="sxs-lookup"><span data-stu-id="04481-215">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="04481-216">Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="04481-216">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="04481-217">Biçimlendiriciler `ConfigureServices` yönteminde kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="04481-217">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="04481-218">Bir model nesne dönüş türü döndürme sırasında döndürülen eylemler `204 No Content` `null` .</span><span class="sxs-lookup"><span data-stu-id="04481-218">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="04481-219">Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="04481-219">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="04481-220">Aşağıdaki kod ve öğesini kaldırır `StringOutputFormatter` `HttpNoContentOutputFormatter` .</span><span class="sxs-lookup"><span data-stu-id="04481-220">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="04481-221">Olmadan `StringOutputFormatter` , YERLEŞIK JSON biçimlendiricisi, `string` dönüş türlerini biçimlendirir.</span><span class="sxs-lookup"><span data-stu-id="04481-221">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="04481-222">Yerleşik JSON biçimlendiricisi kaldırılırsa ve bir XML biçimlendirici varsa, XML biçimlendirici, `string` dönüş türlerini biçimlendirir.</span><span class="sxs-lookup"><span data-stu-id="04481-222">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="04481-223">Aksi takdirde, `string` dönüş türleri döndürülür `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="04481-223">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="04481-224">Olmadan `HttpNoContentOutputFormatter` , null nesneler yapılandırılmış biçimlendirici kullanılarak biçimlendirilir.</span><span class="sxs-lookup"><span data-stu-id="04481-224">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="04481-225">Örnek:</span><span class="sxs-lookup"><span data-stu-id="04481-225">For example:</span></span>

* <span data-ttu-id="04481-226">JSON biçimlendiricisi, gövdesi olan bir yanıt döndürür `null` .</span><span class="sxs-lookup"><span data-stu-id="04481-226">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="04481-227">XML biçimlendiricisi özniteliği ayarlanmış bir boş XML öğesi döndürüyor `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="04481-227">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="04481-228">Yanıt biçimi URL eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="04481-228">Response format URL mappings</span></span>

<span data-ttu-id="04481-229">İstemciler URL 'nin bir parçası olarak belirli bir biçim talep edebilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="04481-229">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="04481-230">Sorgu dizesinde veya yolun bir bölümünde.</span><span class="sxs-lookup"><span data-stu-id="04481-230">In the query string or part of the path.</span></span>
* <span data-ttu-id="04481-231">. Xml veya. JSON gibi formata özgü bir dosya uzantısı kullanarak.</span><span class="sxs-lookup"><span data-stu-id="04481-231">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="04481-232">İstek yolundan eşleme, API 'nin kullandığı rotada belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="04481-232">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="04481-233">Örnek:</span><span class="sxs-lookup"><span data-stu-id="04481-233">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="04481-234">Önceki yol, istenen biçimin isteğe bağlı bir dosya uzantısı olarak belirtilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="04481-234">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="04481-235">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Özniteliği, içindeki biçim değerinin varlığını denetler `RouteData` ve yanıt oluşturulduğunda yanıt biçimini uygun biçimlendirici ile eşler.</span><span class="sxs-lookup"><span data-stu-id="04481-235">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="04481-236">Yol</span><span class="sxs-lookup"><span data-stu-id="04481-236">Route</span></span>        |             <span data-ttu-id="04481-237">Biçimlendirici</span><span class="sxs-lookup"><span data-stu-id="04481-237">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="04481-238">Varsayılan çıkış biçimlendiricisi</span><span class="sxs-lookup"><span data-stu-id="04481-238">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="04481-239">JSON biçimlendiricisi (yapılandırıldıysa)</span><span class="sxs-lookup"><span data-stu-id="04481-239">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="04481-240">XML biçimlendiricisi (yapılandırıldıysa)</span><span class="sxs-lookup"><span data-stu-id="04481-240">The XML formatter (if configured)</span></span>  |
