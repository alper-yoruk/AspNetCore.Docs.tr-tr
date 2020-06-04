---
<span data-ttu-id="b0d4c-101">Başlık: ASP.NET Core Web API yazarındaki yanıt verilerini biçimlendirme: ardalış açıklaması: ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirmeyi öğrenin.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-101">title: Format response data in ASP.NET Core Web API author: ardalis description: Learn how to format response data in ASP.NET Core Web API.</span></span>
<span data-ttu-id="b0d4c-102">MS. Author: Riande MS. Custom: H1Hack27Feb2017 MS. Date: 04/17/2020 No-loc:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-102">ms.author: riande ms.custom: H1Hack27Feb2017 ms.date: 04/17/2020 no-loc:</span></span>
- <span data-ttu-id="b0d4c-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="b0d4c-103">'Blazor'</span></span>
- <span data-ttu-id="b0d4c-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="b0d4c-104">'Identity'</span></span>
- <span data-ttu-id="b0d4c-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="b0d4c-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="b0d4c-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="b0d4c-106">'Razor'</span></span>
- <span data-ttu-id="b0d4c-107">' SignalR ' uid: Web-API/gelişmiş/biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="b0d4c-107">'SignalR' uid: web-api/advanced/formatting</span></span>

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="b0d4c-108">ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="b0d4c-108">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="b0d4c-109">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b0d4c-109">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b0d4c-110">ASP.NET Core MVC, yanıt verilerini biçimlendirme desteğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-110">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="b0d4c-111">Yanıt verileri, belirli biçimler kullanılarak veya istemci tarafından istenen biçime yanıt olarak biçimlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-111">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="b0d4c-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b0d4c-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="b0d4c-113">Formata özgü eylem sonuçları</span><span class="sxs-lookup"><span data-stu-id="b0d4c-113">Format-specific Action Results</span></span>

<span data-ttu-id="b0d4c-114">Bazı eylem sonuç türleri, ve gibi belirli bir biçime özgüdür <xref:Microsoft.AspNetCore.Mvc.JsonResult> <xref:Microsoft.AspNetCore.Mvc.ContentResult> .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-114">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="b0d4c-115">Eylemler, istemci tercihlerinden bağımsız olarak belirli bir biçimde biçimlendirilen sonuçları döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-115">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="b0d4c-116">Örneğin, döndürme `JsonResult` JSON biçimli verileri döndürür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-116">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="b0d4c-117">Döndürme `ContentResult` veya dize, düz metin biçimli dize verileri döndürür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-117">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="b0d4c-118">Belirli bir tür döndürmek için bir eylem gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-118">An action isn't required to return any specific type.</span></span> <span data-ttu-id="b0d4c-119">ASP.NET Core, tüm nesne dönüş değerlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-119">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="b0d4c-120">Tür olmayan nesneleri döndüren eylemlerin sonuçları <xref:Microsoft.AspNetCore.Mvc.IActionResult> , uygun uygulama kullanılarak serileştirilir <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-120">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="b0d4c-121">Daha fazla bilgi için bkz. <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-121">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="b0d4c-122">Yerleşik yardımcı yöntemi <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> JSON biçimli verileri döndürür:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="b0d4c-122">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="b0d4c-123">Örnek indirme, yazarların listesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-123">The sample download returns the list of authors.</span></span> <span data-ttu-id="b0d4c-124">Önceki kodla F12 tarayıcı geliştirici araçları veya [Postman](https://www.getpostman.com/tools) kullanma:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-124">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="b0d4c-125">**Content-Type:** içeren yanıt üst bilgisi `application/json; charset=utf-8` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-125">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="b0d4c-126">İstek üst bilgileri görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-126">The request headers are displayed.</span></span> <span data-ttu-id="b0d4c-127">Örneğin, `Accept` üst bilgi.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-127">For example, the `Accept` header.</span></span> <span data-ttu-id="b0d4c-128">`Accept`Üst bilgi, önceki kod tarafından yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-128">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="b0d4c-129">Düz metin biçimli verileri döndürmek için <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> ve <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> yardımcısını kullanın:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-129">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="b0d4c-130">Yukarıdaki kodda, `Content-Type` döndürülen `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-130">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="b0d4c-131">Şu şekilde bir dize `Content-Type` döndürür `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="b0d4c-131">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="b0d4c-132">Birden çok dönüş türüne sahip eylemler için, döndürün `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-132">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="b0d4c-133">Örneğin, gerçekleştirilen işlemlerin sonucuna göre farklı HTTP durum kodları döndürülüyor.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-133">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="b0d4c-134">İçerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="b0d4c-134">Content negotiation</span></span>

<span data-ttu-id="b0d4c-135">İstemci bir [Accept üst bilgisi](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)belirttiğinde içerik anlaşması oluşur.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-135">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="b0d4c-136">ASP.NET Core tarafından kullanılan varsayılan biçim [JSON](https://json.org/)'dir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-136">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="b0d4c-137">İçerik anlaşması:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-137">Content negotiation is:</span></span>

* <span data-ttu-id="b0d4c-138">Uygulayan <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-138">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="b0d4c-139">Yardımcı metotlarından döndürülen durum koduna özgü eylem sonuçlarına yerleşik olarak.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-139">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="b0d4c-140">Eylem sonuçları yardımcı yöntemleri temel alınır `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-140">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="b0d4c-141">Bir model türü döndürüldüğünde, dönüş türü olur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-141">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="b0d4c-142">Aşağıdaki eylem yöntemi `Ok` ve `NotFound` yardımcı yöntemlerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-142">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="b0d4c-143">Varsayılan olarak, ASP.NET Core `application/json` , `text/json` ve `text/plain` medya türlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-143">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="b0d4c-144">[Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/tools) gibi araçlar, `Accept` istek üst bilgisini dönüş biçimini belirtecek şekilde ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-144">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="b0d4c-145">`Accept`Üst bilgi, sunucunun desteklediği bir tür içerdiğinde, bu tür döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-145">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="b0d4c-146">Sonraki bölümde, ek Biçimlendiriciler ekleme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-146">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="b0d4c-147">Denetleyici eylemleri POCOs (düz eski CLR nesneleri) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-147">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="b0d4c-148">POCO döndürüldüğünde, çalışma zamanı nesneyi sarmalayan bir otomatik olarak oluşturur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-148">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="b0d4c-149">İstemci, biçimlendirilen serileştirilmiş nesneyi alır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-149">The client gets the formatted serialized object.</span></span> <span data-ttu-id="b0d4c-150">Döndürülen nesne ise `null` `204 No Content` yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-150">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="b0d4c-151">Nesne türü döndürülüyor:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-151">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="b0d4c-152">Önceki kodda, geçerli bir yazar diğer adı için bir istek `200 OK` yazarın verileriyle bir yanıt döndürür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-152">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="b0d4c-153">Geçersiz bir diğer ad isteği bir yanıt döndürüyor `204 No Content` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-153">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="b0d4c-154">Accept üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="b0d4c-154">The Accept header</span></span>

<span data-ttu-id="b0d4c-155">İstekte *negotiation* bir `Accept` üst bilgi göründüğünde içerik anlaşması gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-155">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="b0d4c-156">Bir istek bir Accept üst bilgisi içerdiğinde ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-156">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="b0d4c-157">Kabul üst bilgisindeki medya türlerini tercih sırasına göre numaralandırır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-157">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="b0d4c-158">Belirtilen biçimlerden birinde yanıt üretemeyen bir biçimlendirici bulmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-158">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="b0d4c-159">İstemcinin isteğini karşılayabilen bir biçimlendirici bulunmazsa ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-159">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="b0d4c-160">`406 Not Acceptable`Ayarlanmışsa döndürür <xref:Microsoft.AspNetCore.Mvc.MvcOptions> veya-</span><span class="sxs-lookup"><span data-stu-id="b0d4c-160">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="b0d4c-161">Yanıt üreten ilk biçimlendirici bulmayı dener.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-161">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="b0d4c-162">İstenen biçim için bir biçimlendirici yapılandırılmamışsa, nesneyi biçimlendirebileceğini ilk biçimlendirici kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-162">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="b0d4c-163">İstekte hiçbir `Accept` başlık görünürse:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-163">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="b0d4c-164">Nesneyi işleyebilen ilk biçimlendirici, yanıtı seri hale getirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-164">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="b0d4c-165">Hiçbir anlaşma gerçekleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-165">There isn't any negotiation taking place.</span></span> <span data-ttu-id="b0d4c-166">Sunucu hangi biçimin döneceğine karar verir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-166">The server is determining what format to return.</span></span>

<span data-ttu-id="b0d4c-167">Accept üst bilgisi içeriyorsa `*/*` üstbilgi, `RespectBrowserAcceptHeader` true olarak ayarlanmadığı müddetçe yok sayılır <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-167">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="b0d4c-168">Tarayıcılar ve içerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="b0d4c-168">Browsers and content negotiation</span></span>

<span data-ttu-id="b0d4c-169">Tipik API istemcilerinin aksine, Web tarayıcıları `Accept` üst bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-169">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="b0d4c-170">Web tarayıcısı, joker karakterler dahil olmak üzere birçok biçim belirtir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-170">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="b0d4c-171">Varsayılan olarak, Framework isteğin bir tarayıcıdan geldiğini algıladığında:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-171">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="b0d4c-172">`Accept`Üst bilgi yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-172">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="b0d4c-173">Aksi yapılandırılmadığı takdirde içerik JSON içinde döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-173">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="b0d4c-174">Bu, API 'Leri tükettiren tarayıcılarda daha tutarlı bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-174">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="b0d4c-175">Bir uygulamayı tarayıcı onay üstbilgilerini kabul edecek şekilde yapılandırmak için, şu <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="b0d4c-175">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="b0d4c-176">Biçimleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b0d4c-176">Configure formatters</span></span>

<span data-ttu-id="b0d4c-177">Ek biçimleri desteklemesi gereken uygulamalar uygun NuGet paketlerini ekleyebilir ve desteği yapılandırabilir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-177">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="b0d4c-178">Giriş ve çıkış için ayrı biçimlendirme vardır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-178">There are separate formatters for input and output.</span></span> <span data-ttu-id="b0d4c-179">Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-179">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="b0d4c-180">Çıkış biçimleri, yanıtları biçimlendirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-180">Output formatters are used to format responses.</span></span> <span data-ttu-id="b0d4c-181">Özel bir biçimlendirici oluşturma hakkında daha fazla bilgi için bkz. [özel Formatlayıcılar](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="b0d4c-181">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="b0d4c-182">XML biçimi desteği ekle</span><span class="sxs-lookup"><span data-stu-id="b0d4c-182">Add XML format support</span></span>

<span data-ttu-id="b0d4c-183">Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="b0d4c-183">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="b0d4c-184">Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-184">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b0d4c-185">Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürür `Accept` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-185">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="b0d4c-186">System. Text. JSON tabanlı formatlayıcıları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="b0d4c-186">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="b0d4c-187">`System.Text.Json`Tabanlı formatlayıcılar için özellikler kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-187">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b0d4c-188">İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-188">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b0d4c-189">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-189">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="b0d4c-190">Newtonsoft. JSON tabanlı JSON biçimi desteği ekleyin</span><span class="sxs-lookup"><span data-stu-id="b0d4c-190">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="b0d4c-191">ASP.NET Core 3,0 ' dan önce, varsayılan olarak kullanılan JSON formatlayıcıları paket kullanılarak uygulanır `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-191">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="b0d4c-192">ASP.NET Core 3,0 veya sonraki bir sürümde, varsayılan JSON formatlayıcıları temel alınır `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-192">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="b0d4c-193">`Newtonsoft.Json`Temel biçimlendirme ve özellikler için destek, [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketi yüklenerek ve içinde yapılandırılarak kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-193">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="b0d4c-194">Bazı özellikler `System.Text.Json` , tabanlı formatlayıcılar ile düzgün çalışmayabilir ve `Newtonsoft.Json` tabanlı formatlara bir başvuru gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-194">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="b0d4c-195">`Newtonsoft.Json`Uygulama şu durumlarda tabanlı formatlayıcıları kullanmaya devam edin:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-195">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="b0d4c-196">`Newtonsoft.Json`Öznitelikleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-196">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="b0d4c-197">Örneğin `[JsonProperty]` veya `[JsonIgnore]` olabilir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-197">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="b0d4c-198">Serileştirme ayarlarını özelleştirir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-198">Customizes the serialization settings.</span></span>
* <span data-ttu-id="b0d4c-199">, Tarafından sağlanan özellikleri kullanır `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-199">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="b0d4c-200">Yapılandırır `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-200">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="b0d4c-201">ASP.NET Core 3,0 ' dan önce, `JsonResult.SerializerSettings` öğesine özgü bir örneğini kabul eder `JsonSerializerSettings` `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-201">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="b0d4c-202">[Openapı](<xref:tutorials/web-api-help-pages-using-swagger>) belgeleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-202">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="b0d4c-203">`Newtonsoft.Json`Tabanlı formatlayıcılar için özellikler şu kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="b0d4c-203">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="b0d4c-204">İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-204">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="b0d4c-205">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-205">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="b0d4c-206">XML biçimi desteği ekle</span><span class="sxs-lookup"><span data-stu-id="b0d4c-206">Add XML format support</span></span>

<span data-ttu-id="b0d4c-207">XML biçimlendirme, [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-207">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="b0d4c-208">Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="b0d4c-208">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="b0d4c-209">Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-209">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="b0d4c-210">Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürmelidir `Accept` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-210">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="b0d4c-211">Biçim belirtin</span><span class="sxs-lookup"><span data-stu-id="b0d4c-211">Specify a format</span></span>

<span data-ttu-id="b0d4c-212">Yanıt biçimlerini kısıtlamak için [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtreyi uygulayın.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-212">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="b0d4c-213">Çoğu [filtre](xref:mvc/controllers/filters)gibi `[Produces]` eylem, denetleyici veya genel kapsamda de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-213">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="b0d4c-214">Önceki [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-214">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="b0d4c-215">Denetleyici içindeki tüm eylemleri JSON biçimli yanıtları döndürecek şekilde zorlar.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-215">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="b0d4c-216">Diğer formatlayıcılar yapılandırıldıysa ve istemci farklı bir biçim belirtiyorsa JSON döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-216">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="b0d4c-217">Daha fazla bilgi için bkz. [Filtreler](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="b0d4c-217">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="b0d4c-218">Özel durum formatları</span><span class="sxs-lookup"><span data-stu-id="b0d4c-218">Special case formatters</span></span>

<span data-ttu-id="b0d4c-219">Bazı özel durumlar, yerleşik formatlayıcılar kullanılarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-219">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="b0d4c-220">Varsayılan olarak, `string` dönüş türleri *metin/düz* olarak biçimlendirilir (üst bilgi ile isteniyorsa*metin/html* `Accept` ).</span><span class="sxs-lookup"><span data-stu-id="b0d4c-220">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="b0d4c-221">Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-221">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="b0d4c-222">Biçimlendiriciler `ConfigureServices` yönteminde kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-222">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="b0d4c-223">Bir model nesne dönüş türü döndürme sırasında döndürülen eylemler `204 No Content` `null` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-223">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="b0d4c-224">Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-224">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="b0d4c-225">Aşağıdaki kod ve öğesini kaldırır `StringOutputFormatter` `HttpNoContentOutputFormatter` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-225">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="b0d4c-226">Olmadan `StringOutputFormatter` , YERLEŞIK JSON biçimlendiricisi, `string` dönüş türlerini biçimlendirir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-226">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="b0d4c-227">Yerleşik JSON biçimlendiricisi kaldırılırsa ve bir XML biçimlendirici varsa, XML biçimlendirici, `string` dönüş türlerini biçimlendirir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-227">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="b0d4c-228">Aksi takdirde, `string` dönüş türleri döndürülür `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-228">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="b0d4c-229">Olmadan `HttpNoContentOutputFormatter` , null nesneler yapılandırılmış biçimlendirici kullanılarak biçimlendirilir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-229">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="b0d4c-230">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-230">For example:</span></span>

* <span data-ttu-id="b0d4c-231">JSON biçimlendiricisi, gövdesi olan bir yanıt döndürür `null` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-231">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="b0d4c-232">XML biçimlendiricisi özniteliği ayarlanmış bir boş XML öğesi döndürüyor `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="b0d4c-232">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="b0d4c-233">Yanıt biçimi URL eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="b0d4c-233">Response format URL mappings</span></span>

<span data-ttu-id="b0d4c-234">İstemciler URL 'nin bir parçası olarak belirli bir biçim talep edebilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-234">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="b0d4c-235">Sorgu dizesinde veya yolun bir bölümünde.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-235">In the query string or part of the path.</span></span>
* <span data-ttu-id="b0d4c-236">. Xml veya. JSON gibi formata özgü bir dosya uzantısı kullanarak.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-236">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="b0d4c-237">İstek yolundan eşleme, API 'nin kullandığı rotada belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-237">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="b0d4c-238">Örnek:</span><span class="sxs-lookup"><span data-stu-id="b0d4c-238">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="b0d4c-239">Önceki yol, istenen biçimin isteğe bağlı bir dosya uzantısı olarak belirtilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-239">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="b0d4c-240">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Özniteliği, içindeki biçim değerinin varlığını denetler `RouteData` ve yanıt oluşturulduğunda yanıt biçimini uygun biçimlendirici ile eşler.</span><span class="sxs-lookup"><span data-stu-id="b0d4c-240">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="b0d4c-241">Yol</span><span class="sxs-lookup"><span data-stu-id="b0d4c-241">Route</span></span>        |             <span data-ttu-id="b0d4c-242">Biçimlendirici</span><span class="sxs-lookup"><span data-stu-id="b0d4c-242">Formatter</span></span>              |
|------------------------|------------------------------------|
|   `/api/products/5`    |    <span data-ttu-id="b0d4c-243">Varsayılan çıkış biçimlendiricisi</span><span class="sxs-lookup"><span data-stu-id="b0d4c-243">The default output formatter</span></span>    |
| `/api/products/5.json` | <span data-ttu-id="b0d4c-244">JSON biçimlendiricisi (yapılandırıldıysa)</span><span class="sxs-lookup"><span data-stu-id="b0d4c-244">The JSON formatter (if configured)</span></span> |
| `/api/products/5.xml`  | <span data-ttu-id="b0d4c-245">XML biçimlendiricisi (yapılandırıldıysa)</span><span class="sxs-lookup"><span data-stu-id="b0d4c-245">The XML formatter (if configured)</span></span>  |
