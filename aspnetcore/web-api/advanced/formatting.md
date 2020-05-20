---
<span data-ttu-id="ca38c-101">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-102">'Blazor'</span></span>
- <span data-ttu-id="ca38c-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-103">'Identity'</span></span>
- <span data-ttu-id="ca38c-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-105">'Razor'</span></span>
- <span data-ttu-id="ca38c-106">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-106">'SignalR' uid:</span></span> 

---
# <a name="format-response-data-in-aspnet-core-web-api"></a><span data-ttu-id="ca38c-107">ASP.NET Core Web API 'sindeki yanıt verilerini biçimlendirme</span><span class="sxs-lookup"><span data-stu-id="ca38c-107">Format response data in ASP.NET Core Web API</span></span>

<span data-ttu-id="ca38c-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ca38c-108">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="ca38c-109">ASP.NET Core MVC, yanıt verilerini biçimlendirme desteğine sahiptir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-109">ASP.NET Core MVC has support for formatting response data.</span></span> <span data-ttu-id="ca38c-110">Yanıt verileri, belirli biçimler kullanılarak veya istemci tarafından istenen biçime yanıt olarak biçimlendirilebilir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-110">Response data can be formatted using specific formats or in response to client requested format.</span></span>

<span data-ttu-id="ca38c-111">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ca38c-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/advanced/formatting) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="format-specific-action-results"></a><span data-ttu-id="ca38c-112">Formata özgü eylem sonuçları</span><span class="sxs-lookup"><span data-stu-id="ca38c-112">Format-specific Action Results</span></span>

<span data-ttu-id="ca38c-113">Bazı eylem sonuç türleri, ve gibi belirli bir biçime özgüdür <xref:Microsoft.AspNetCore.Mvc.JsonResult> <xref:Microsoft.AspNetCore.Mvc.ContentResult> .</span><span class="sxs-lookup"><span data-stu-id="ca38c-113">Some action result types are specific to a particular format, such as <xref:Microsoft.AspNetCore.Mvc.JsonResult> and <xref:Microsoft.AspNetCore.Mvc.ContentResult>.</span></span> <span data-ttu-id="ca38c-114">Eylemler, istemci tercihlerinden bağımsız olarak belirli bir biçimde biçimlendirilen sonuçları döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-114">Actions can return results that are formatted in a particular format, regardless of client preferences.</span></span> <span data-ttu-id="ca38c-115">Örneğin, döndürme `JsonResult` JSON biçimli verileri döndürür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-115">For example, returning `JsonResult` returns JSON-formatted data.</span></span> <span data-ttu-id="ca38c-116">Döndürme `ContentResult` veya dize, düz metin biçimli dize verileri döndürür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-116">Returning `ContentResult` or a string returns plain-text-formatted string data.</span></span>

<span data-ttu-id="ca38c-117">Belirli bir tür döndürmek için bir eylem gerekli değildir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-117">An action isn't required to return any specific type.</span></span> <span data-ttu-id="ca38c-118">ASP.NET Core, tüm nesne dönüş değerlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="ca38c-118">ASP.NET Core supports any object return value.</span></span>  <span data-ttu-id="ca38c-119">Tür olmayan nesneleri döndüren eylemlerin sonuçları <xref:Microsoft.AspNetCore.Mvc.IActionResult> , uygun uygulama kullanılarak serileştirilir <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="ca38c-119">Results from actions that return objects that are not <xref:Microsoft.AspNetCore.Mvc.IActionResult> types are serialized using the appropriate <xref:Microsoft.AspNetCore.Mvc.Formatters.IOutputFormatter> implementation.</span></span> <span data-ttu-id="ca38c-120">Daha fazla bilgi için bkz. <xref:web-api/action-return-types>.</span><span class="sxs-lookup"><span data-stu-id="ca38c-120">For more information, see <xref:web-api/action-return-types>.</span></span>

<span data-ttu-id="ca38c-121">Yerleşik yardımcı yöntemi <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> JSON biçimli verileri döndürür:[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span><span class="sxs-lookup"><span data-stu-id="ca38c-121">The built-in helper method <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> returns JSON-formatted data: [!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_get)]</span></span>

<span data-ttu-id="ca38c-122">Örnek indirme, yazarların listesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-122">The sample download returns the list of authors.</span></span> <span data-ttu-id="ca38c-123">Önceki kodla F12 tarayıcı geliştirici araçları veya [Postman](https://www.getpostman.com/tools) kullanma:</span><span class="sxs-lookup"><span data-stu-id="ca38c-123">Using the F12 browser developer tools or [Postman](https://www.getpostman.com/tools) with the previous code:</span></span>

* <span data-ttu-id="ca38c-124">**Content-Type:** içeren yanıt üst bilgisi `application/json; charset=utf-8` görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-124">The response header containing **content-type:** `application/json; charset=utf-8` is displayed.</span></span>
* <span data-ttu-id="ca38c-125">İstek üst bilgileri görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-125">The request headers are displayed.</span></span> <span data-ttu-id="ca38c-126">Örneğin, `Accept` üst bilgi.</span><span class="sxs-lookup"><span data-stu-id="ca38c-126">For example, the `Accept` header.</span></span> <span data-ttu-id="ca38c-127">`Accept`Üst bilgi, önceki kod tarafından yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-127">The `Accept` header is ignored by the preceding code.</span></span>

<span data-ttu-id="ca38c-128">Düz metin biçimli verileri döndürmek için <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> ve <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> yardımcısını kullanın:</span><span class="sxs-lookup"><span data-stu-id="ca38c-128">To return plain text formatted data, use <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> and the <xref:Microsoft.AspNetCore.Mvc.ContentResult.Content> helper:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_about)]

<span data-ttu-id="ca38c-129">Yukarıdaki kodda, `Content-Type` döndürülen `text/plain` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-129">In the preceding code, the `Content-Type` returned is `text/plain`.</span></span> <span data-ttu-id="ca38c-130">Şu şekilde bir dize `Content-Type` döndürür `text/plain` :</span><span class="sxs-lookup"><span data-stu-id="ca38c-130">Returning a string delivers `Content-Type` of `text/plain`:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_string)]

<span data-ttu-id="ca38c-131">Birden çok dönüş türüne sahip eylemler için, döndürün `IActionResult` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-131">For actions with multiple return types, return `IActionResult`.</span></span> <span data-ttu-id="ca38c-132">Örneğin, gerçekleştirilen işlemlerin sonucuna göre farklı HTTP durum kodları döndürülüyor.</span><span class="sxs-lookup"><span data-stu-id="ca38c-132">For example, returning different HTTP status codes based on the result of operations performed.</span></span>

## <a name="content-negotiation"></a><span data-ttu-id="ca38c-133">İçerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="ca38c-133">Content negotiation</span></span>

<span data-ttu-id="ca38c-134">İstemci bir [Accept üst bilgisi](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)belirttiğinde içerik anlaşması oluşur.</span><span class="sxs-lookup"><span data-stu-id="ca38c-134">Content negotiation occurs when the client specifies an [Accept header](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).</span></span> <span data-ttu-id="ca38c-135">ASP.NET Core tarafından kullanılan varsayılan biçim [JSON](https://json.org/)'dir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-135">The default format used by ASP.NET Core is [JSON](https://json.org/).</span></span> <span data-ttu-id="ca38c-136">İçerik anlaşması:</span><span class="sxs-lookup"><span data-stu-id="ca38c-136">Content negotiation is:</span></span>

* <span data-ttu-id="ca38c-137">Uygulayan <xref:Microsoft.AspNetCore.Mvc.ObjectResult> .</span><span class="sxs-lookup"><span data-stu-id="ca38c-137">Implemented by <xref:Microsoft.AspNetCore.Mvc.ObjectResult>.</span></span>
* <span data-ttu-id="ca38c-138">Yardımcı metotlarından döndürülen durum koduna özgü eylem sonuçlarına yerleşik olarak.</span><span class="sxs-lookup"><span data-stu-id="ca38c-138">Built into the status code-specific action results returned from the helper methods.</span></span> <span data-ttu-id="ca38c-139">Eylem sonuçları yardımcı yöntemleri temel alınır `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-139">The action results helper methods are based on `ObjectResult`.</span></span>

<span data-ttu-id="ca38c-140">Bir model türü döndürüldüğünde, dönüş türü olur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-140">When a model type is returned,  the return type is `ObjectResult`.</span></span>

<span data-ttu-id="ca38c-141">Aşağıdaki eylem yöntemi `Ok` ve `NotFound` yardımcı yöntemlerini kullanır:</span><span class="sxs-lookup"><span data-stu-id="ca38c-141">The following action method uses the `Ok` and `NotFound` helper methods:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_search)]

<span data-ttu-id="ca38c-142">Varsayılan olarak, ASP.NET Core `application/json` , `text/json` ve `text/plain` medya türlerini destekler.</span><span class="sxs-lookup"><span data-stu-id="ca38c-142">By default, ASP.NET Core supports `application/json`, `text/json`, and `text/plain` media types.</span></span> <span data-ttu-id="ca38c-143">[Fiddler](https://www.telerik.com/fiddler) veya [Postman](https://www.getpostman.com/tools) gibi araçlar, `Accept` istek üst bilgisini dönüş biçimini belirtecek şekilde ayarlayabilir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-143">Tools such as [Fiddler](https://www.telerik.com/fiddler) or [Postman](https://www.getpostman.com/tools) can set the `Accept` request header to specify the return format.</span></span> <span data-ttu-id="ca38c-144">`Accept`Üst bilgi, sunucunun desteklediği bir tür içerdiğinde, bu tür döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-144">When the `Accept` header contains a type the server supports, that type is returned.</span></span> <span data-ttu-id="ca38c-145">Sonraki bölümde, ek Biçimlendiriciler ekleme gösterilmektedir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-145">The next section shows how to add additional formatters.</span></span>

<span data-ttu-id="ca38c-146">Denetleyici eylemleri POCOs (düz eski CLR nesneleri) döndürebilir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-146">Controller actions can return POCOs (Plain Old CLR Objects).</span></span> <span data-ttu-id="ca38c-147">POCO döndürüldüğünde, çalışma zamanı nesneyi sarmalayan bir otomatik olarak oluşturur `ObjectResult` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-147">When a POCO is returned, the runtime automatically creates an `ObjectResult` that wraps the object.</span></span> <span data-ttu-id="ca38c-148">İstemci, biçimlendirilen serileştirilmiş nesneyi alır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-148">The client gets the formatted serialized object.</span></span> <span data-ttu-id="ca38c-149">Döndürülen nesne ise `null` `204 No Content` yanıt döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-149">If the object being returned is `null`, a `204 No Content` response is returned.</span></span>

<span data-ttu-id="ca38c-150">Nesne türü döndürülüyor:</span><span class="sxs-lookup"><span data-stu-id="ca38c-150">Returning an object type:</span></span>

[!code-csharp[](./formatting/sample/Controllers/AuthorsController.cs?name=snippet_alias)]

<span data-ttu-id="ca38c-151">Önceki kodda, geçerli bir yazar diğer adı için bir istek `200 OK` yazarın verileriyle bir yanıt döndürür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-151">In the preceding code, a request for a valid author alias returns a `200 OK` response with the author's data.</span></span> <span data-ttu-id="ca38c-152">Geçersiz bir diğer ad isteği bir yanıt döndürüyor `204 No Content` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-152">A request for an invalid alias returns a `204 No Content` response.</span></span>

### <a name="the-accept-header"></a><span data-ttu-id="ca38c-153">Accept üst bilgisi</span><span class="sxs-lookup"><span data-stu-id="ca38c-153">The Accept header</span></span>

<span data-ttu-id="ca38c-154">İstekte *negotiation* bir `Accept` üst bilgi göründüğünde içerik anlaşması gerçekleşir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-154">Content *negotiation* takes place when an `Accept` header appears in the request.</span></span> <span data-ttu-id="ca38c-155">Bir istek bir Accept üst bilgisi içerdiğinde ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca38c-155">When a request contains an accept header, ASP.NET Core:</span></span>

* <span data-ttu-id="ca38c-156">Kabul üst bilgisindeki medya türlerini tercih sırasına göre numaralandırır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-156">Enumerates the media types in the accept header in preference order.</span></span>
* <span data-ttu-id="ca38c-157">Belirtilen biçimlerden birinde yanıt üretemeyen bir biçimlendirici bulmaya çalışır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-157">Tries to find a formatter that can produce a response in one of the formats specified.</span></span>

<span data-ttu-id="ca38c-158">İstemcinin isteğini karşılayabilen bir biçimlendirici bulunmazsa ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ca38c-158">If no formatter is found that can satisfy the client's request, ASP.NET Core:</span></span>

* <span data-ttu-id="ca38c-159">`406 Not Acceptable`Ayarlanmışsa döndürür <xref:Microsoft.AspNetCore.Mvc.MvcOptions> veya-</span><span class="sxs-lookup"><span data-stu-id="ca38c-159">Returns `406 Not Acceptable` if <xref:Microsoft.AspNetCore.Mvc.MvcOptions> has been set, or -</span></span>
* <span data-ttu-id="ca38c-160">Yanıt üreten ilk biçimlendirici bulmayı dener.</span><span class="sxs-lookup"><span data-stu-id="ca38c-160">Tries to find the first formatter that can produce a response.</span></span>

<span data-ttu-id="ca38c-161">İstenen biçim için bir biçimlendirici yapılandırılmamışsa, nesneyi biçimlendirebileceğini ilk biçimlendirici kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-161">If no formatter is configured for the requested format, the first formatter that can format the object is used.</span></span> <span data-ttu-id="ca38c-162">İstekte hiçbir `Accept` başlık görünürse:</span><span class="sxs-lookup"><span data-stu-id="ca38c-162">If no `Accept` header appears in the request:</span></span>

* <span data-ttu-id="ca38c-163">Nesneyi işleyebilen ilk biçimlendirici, yanıtı seri hale getirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-163">The first formatter that can handle the object is used to serialize the response.</span></span>
* <span data-ttu-id="ca38c-164">Hiçbir anlaşma gerçekleşmiyor.</span><span class="sxs-lookup"><span data-stu-id="ca38c-164">There isn't any negotiation taking place.</span></span> <span data-ttu-id="ca38c-165">Sunucu hangi biçimin döneceğine karar verir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-165">The server is determining what format to return.</span></span>

<span data-ttu-id="ca38c-166">Accept üst bilgisi içeriyorsa `*/*` üstbilgi, `RespectBrowserAcceptHeader` true olarak ayarlanmadığı müddetçe yok sayılır <xref:Microsoft.AspNetCore.Mvc.MvcOptions> .</span><span class="sxs-lookup"><span data-stu-id="ca38c-166">If the Accept header contains `*/*`, the Header is ignored unless `RespectBrowserAcceptHeader` is set to true on <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span>

### <a name="browsers-and-content-negotiation"></a><span data-ttu-id="ca38c-167">Tarayıcılar ve içerik anlaşması</span><span class="sxs-lookup"><span data-stu-id="ca38c-167">Browsers and content negotiation</span></span>

<span data-ttu-id="ca38c-168">Tipik API istemcilerinin aksine, Web tarayıcıları `Accept` üst bilgileri sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca38c-168">Unlike typical API clients, web browsers supply `Accept` headers.</span></span> <span data-ttu-id="ca38c-169">Web tarayıcısı, joker karakterler dahil olmak üzere birçok biçim belirtir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-169">Web browser specify many formats, including wildcards.</span></span> <span data-ttu-id="ca38c-170">Varsayılan olarak, Framework isteğin bir tarayıcıdan geldiğini algıladığında:</span><span class="sxs-lookup"><span data-stu-id="ca38c-170">By default, when the framework detects that the request is coming from a browser:</span></span>

* <span data-ttu-id="ca38c-171">`Accept`Üst bilgi yok sayılır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-171">The `Accept` header is ignored.</span></span>
* <span data-ttu-id="ca38c-172">Aksi yapılandırılmadığı takdirde içerik JSON içinde döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-172">The content is returned in JSON, unless otherwise configured.</span></span>

<span data-ttu-id="ca38c-173">Bu, API 'Leri tükettiren tarayıcılarda daha tutarlı bir deneyim sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca38c-173">This provides a more consistent experience across browsers when consuming APIs.</span></span>

<span data-ttu-id="ca38c-174">Bir uygulamayı tarayıcı onay üstbilgilerini kabul edecek şekilde yapılandırmak için, şu <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> şekilde ayarlayın `true` :</span><span class="sxs-lookup"><span data-stu-id="ca38c-174">To configure an app to honor browser accept headers, set <xref:Microsoft.AspNetCore.Mvc.MvcOptions.RespectBrowserAcceptHeader> to `true`:</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupRespectBrowserAcceptHeader.cs?name=snippet)]
::: moniker-end

### <a name="configure-formatters"></a><span data-ttu-id="ca38c-175">Biçimleri yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ca38c-175">Configure formatters</span></span>

<span data-ttu-id="ca38c-176">Ek biçimleri desteklemesi gereken uygulamalar uygun NuGet paketlerini ekleyebilir ve desteği yapılandırabilir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-176">Apps that need to support additional formats can add the appropriate NuGet packages and configure support.</span></span> <span data-ttu-id="ca38c-177">Giriş ve çıkış için ayrı biçimlendirme vardır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-177">There are separate formatters for input and output.</span></span> <span data-ttu-id="ca38c-178">Giriş formatlayıcıları [model bağlama](xref:mvc/models/model-binding)tarafından kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-178">Input formatters are used by [Model Binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="ca38c-179">Çıkış biçimleri, yanıtları biçimlendirmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-179">Output formatters are used to format responses.</span></span> <span data-ttu-id="ca38c-180">Özel bir biçimlendirici oluşturma hakkında daha fazla bilgi için bkz. [özel Formatlayıcılar](xref:web-api/advanced/custom-formatters).</span><span class="sxs-lookup"><span data-stu-id="ca38c-180">For information on creating a custom formatter, see [Custom Formatters](xref:web-api/advanced/custom-formatters).</span></span>

::: moniker range=">= aspnetcore-3.0"

### <a name="add-xml-format-support"></a><span data-ttu-id="ca38c-181">XML biçimi desteği ekle</span><span class="sxs-lookup"><span data-stu-id="ca38c-181">Add XML format support</span></span>

<span data-ttu-id="ca38c-182">Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="ca38c-182">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/3.0sample/Startup.cs?name=snippet)]

<span data-ttu-id="ca38c-183">Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-183">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="ca38c-184">Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürür `Accept` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-184">When using the preceding code, controller methods return the appropriate format based on the request's `Accept` header.</span></span>

### <a name="configure-systemtextjson-based-formatters"></a><span data-ttu-id="ca38c-185">System. Text. JSON tabanlı formatlayıcıları yapılandırma</span><span class="sxs-lookup"><span data-stu-id="ca38c-185">Configure System.Text.Json-based formatters</span></span>

<span data-ttu-id="ca38c-186">`System.Text.Json`Tabanlı formatlayıcılar için özellikler kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-186">Features for the `System.Text.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.JsonOptions.SerializerOptions`.</span></span>

```csharp
services.AddControllers().AddJsonOptions(options =>
{
    // Use the default property (Pascal) casing.
    options.JsonSerializerOptions.PropertyNamingPolicy = null;

    // Configure a custom converter.
    options.JsonSerializerOptions.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="ca38c-187">İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-187">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="ca38c-188">Örnek:</span><span class="sxs-lookup"><span data-stu-id="ca38c-188">For example:</span></span>

```csharp
public IActionResult Get()
{
    return Json(model, new JsonSerializerOptions
    {
        options.WriteIndented = true,
    });
}
```

### <a name="add-newtonsoftjson-based-json-format-support"></a><span data-ttu-id="ca38c-189">Newtonsoft. JSON tabanlı JSON biçimi desteği ekleyin</span><span class="sxs-lookup"><span data-stu-id="ca38c-189">Add Newtonsoft.Json-based JSON format support</span></span>

<span data-ttu-id="ca38c-190">ASP.NET Core 3,0 ' dan önce, varsayılan olarak kullanılan JSON formatlayıcıları paket kullanılarak uygulanır `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-190">Prior to ASP.NET Core 3.0, the default used JSON formatters implemented using the `Newtonsoft.Json` package.</span></span> <span data-ttu-id="ca38c-191">ASP.NET Core 3,0 veya sonraki bir sürümde, varsayılan JSON formatlayıcıları temel alınır `System.Text.Json` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-191">In ASP.NET Core 3.0 or later, the default JSON formatters are based on `System.Text.Json`.</span></span> <span data-ttu-id="ca38c-192">`Newtonsoft.Json`Temel biçimlendirme ve özellikler için destek, [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet paketi yüklenerek ve içinde yapılandırılarak kullanılabilir `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-192">Support for `Newtonsoft.Json` based formatters and features is available by installing the [`Microsoft.AspNetCore.Mvc.NewtonsoftJson`](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package and configuring it in `Startup.ConfigureServices`.</span></span>

[!code-csharp[](./formatting/3.0sample/StartupNewtonsoftJson.cs?name=snippet)]

<span data-ttu-id="ca38c-193">Bazı özellikler `System.Text.Json` , tabanlı formatlayıcılar ile düzgün çalışmayabilir ve `Newtonsoft.Json` tabanlı formatlara bir başvuru gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-193">Some features may not work well with `System.Text.Json`-based formatters and require a reference to the `Newtonsoft.Json`-based formatters.</span></span> <span data-ttu-id="ca38c-194">`Newtonsoft.Json`Uygulama şu durumlarda tabanlı formatlayıcıları kullanmaya devam edin:</span><span class="sxs-lookup"><span data-stu-id="ca38c-194">Continue using the `Newtonsoft.Json`-based formatters if the app:</span></span>

* <span data-ttu-id="ca38c-195">`Newtonsoft.Json`Öznitelikleri kullanır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-195">Uses `Newtonsoft.Json` attributes.</span></span> <span data-ttu-id="ca38c-196">Örneğin `[JsonProperty]` veya `[JsonIgnore]` olabilir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-196">For example, `[JsonProperty]` or `[JsonIgnore]`.</span></span>
* <span data-ttu-id="ca38c-197">Serileştirme ayarlarını özelleştirir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-197">Customizes the serialization settings.</span></span>
* <span data-ttu-id="ca38c-198">, Tarafından sağlanan özellikleri kullanır `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-198">Relies on features that `Newtonsoft.Json` provides.</span></span>
* <span data-ttu-id="ca38c-199">Yapılandırır `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-199">Configures `Microsoft.AspNetCore.Mvc.JsonResult.SerializerSettings`.</span></span> <span data-ttu-id="ca38c-200">ASP.NET Core 3,0 ' dan önce, `JsonResult.SerializerSettings` öğesine özgü bir örneğini kabul eder `JsonSerializerSettings` `Newtonsoft.Json` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-200">Prior to ASP.NET Core 3.0, `JsonResult.SerializerSettings` accepts an instance of `JsonSerializerSettings` that is specific to `Newtonsoft.Json`.</span></span>
* <span data-ttu-id="ca38c-201">[Openapı](<xref:tutorials/web-api-help-pages-using-swagger>) belgeleri oluşturur.</span><span class="sxs-lookup"><span data-stu-id="ca38c-201">Generates [OpenAPI](<xref:tutorials/web-api-help-pages-using-swagger>) documentation.</span></span>

<span data-ttu-id="ca38c-202">`Newtonsoft.Json`Tabanlı formatlayıcılar için özellikler şu kullanılarak yapılandırılabilir `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings` :</span><span class="sxs-lookup"><span data-stu-id="ca38c-202">Features for the `Newtonsoft.Json`-based formatters can be configured using `Microsoft.AspNetCore.Mvc.MvcNewtonsoftJsonOptions.SerializerSettings`:</span></span>

```csharp
services.AddControllers().AddNewtonsoftJson(options =>
{
    // Use the default property (Pascal) casing
    options.SerializerSettings.ContractResolver = new DefaultContractResolver();

    // Configure a custom converter
    options.SerializerSettings.Converters.Add(new MyCustomJsonConverter());
});
```

<span data-ttu-id="ca38c-203">İşlem başına temelinde çıkış serileştirme seçenekleri kullanılarak yapılandırılabilir `JsonResult` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-203">Output serialization options, on a per-action basis, can be configured using `JsonResult`.</span></span> <span data-ttu-id="ca38c-204">Örnek:</span><span class="sxs-lookup"><span data-stu-id="ca38c-204">For example:</span></span>

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

### <a name="add-xml-format-support"></a><span data-ttu-id="ca38c-205">XML biçimi desteği ekle</span><span class="sxs-lookup"><span data-stu-id="ca38c-205">Add XML format support</span></span>

<span data-ttu-id="ca38c-206">XML biçimlendirme, [Microsoft. AspNetCore. Mvc. Formatters. xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-206">XML formatting requires the [Microsoft.AspNetCore.Mvc.Formatters.Xml](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Formatters.Xml/) NuGet package.</span></span>

<span data-ttu-id="ca38c-207">Kullanılarak uygulanan XML formatlayıcıları <xref:System.Xml.Serialization.XmlSerializer> , çağırarak yapılandırılır <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*> :</span><span class="sxs-lookup"><span data-stu-id="ca38c-207">XML formatters implemented using <xref:System.Xml.Serialization.XmlSerializer> are configured by calling <xref:Microsoft.Extensions.DependencyInjection.MvcXmlMvcBuilderExtensions.AddXmlSerializerFormatters*>:</span></span>

[!code-csharp[](./formatting/sample/Startup.cs?name=snippet)]

<span data-ttu-id="ca38c-208">Yukarıdaki kod, kullanılarak sonuçları seri hale getirir `XmlSerializer` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-208">The preceding code serializes results using `XmlSerializer`.</span></span>

<span data-ttu-id="ca38c-209">Önceki kodu kullanırken, denetleyici yöntemleri isteğin üstbilgisine göre uygun biçimi döndürmelidir `Accept` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-209">When using the preceding code, controller methods should return the appropriate format based on the request's `Accept` header.</span></span>

::: moniker-end

### <a name="specify-a-format"></a><span data-ttu-id="ca38c-210">Biçim belirtin</span><span class="sxs-lookup"><span data-stu-id="ca38c-210">Specify a format</span></span>

<span data-ttu-id="ca38c-211">Yanıt biçimlerini kısıtlamak için [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtreyi uygulayın.</span><span class="sxs-lookup"><span data-stu-id="ca38c-211">To restrict the response formats, apply the [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter.</span></span> <span data-ttu-id="ca38c-212">Çoğu [filtre](xref:mvc/controllers/filters)gibi `[Produces]` eylem, denetleyici veya genel kapsamda de uygulanabilir:</span><span class="sxs-lookup"><span data-stu-id="ca38c-212">Like most [Filters](xref:mvc/controllers/filters), `[Produces]` can be applied at the action, controller, or global scope:</span></span>

[!code-csharp[](./formatting/3.0sample/Controllers/WeatherForecastController.cs?name=snippet)]

<span data-ttu-id="ca38c-213">Önceki [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filtre:</span><span class="sxs-lookup"><span data-stu-id="ca38c-213">The preceding [`[Produces]`](xref:Microsoft.AspNetCore.Mvc.ProducesAttribute) filter:</span></span>

* <span data-ttu-id="ca38c-214">Denetleyici içindeki tüm eylemleri JSON biçimli yanıtları döndürecek şekilde zorlar.</span><span class="sxs-lookup"><span data-stu-id="ca38c-214">Forces all actions within the controller to return JSON-formatted responses.</span></span>
* <span data-ttu-id="ca38c-215">Diğer formatlayıcılar yapılandırıldıysa ve istemci farklı bir biçim belirtiyorsa JSON döndürülür.</span><span class="sxs-lookup"><span data-stu-id="ca38c-215">If other formatters are configured and the client specifies a different format, JSON is returned.</span></span>

<span data-ttu-id="ca38c-216">Daha fazla bilgi için bkz. [Filtreler](xref:mvc/controllers/filters).</span><span class="sxs-lookup"><span data-stu-id="ca38c-216">For more information, see [Filters](xref:mvc/controllers/filters).</span></span>

### <a name="special-case-formatters"></a><span data-ttu-id="ca38c-217">Özel durum formatları</span><span class="sxs-lookup"><span data-stu-id="ca38c-217">Special case formatters</span></span>

<span data-ttu-id="ca38c-218">Bazı özel durumlar, yerleşik formatlayıcılar kullanılarak uygulanır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-218">Some special cases are implemented using built-in formatters.</span></span> <span data-ttu-id="ca38c-219">Varsayılan olarak, `string` dönüş türleri *metin/düz* olarak biçimlendirilir (üst bilgi ile isteniyorsa*metin/html* `Accept` ).</span><span class="sxs-lookup"><span data-stu-id="ca38c-219">By default, `string` return types are formatted as *text/plain* (*text/html* if requested via the `Accept` header).</span></span> <span data-ttu-id="ca38c-220">Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="ca38c-220">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.StringOutputFormatter>.</span></span> <span data-ttu-id="ca38c-221">Biçimlendiriciler `ConfigureServices` yönteminde kaldırılır.</span><span class="sxs-lookup"><span data-stu-id="ca38c-221">Formatters are removed in the `ConfigureServices` method.</span></span> <span data-ttu-id="ca38c-222">Bir model nesne dönüş türü döndürme sırasında döndürülen eylemler `204 No Content` `null` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-222">Actions that have a model object return type return `204 No Content` when returning `null`.</span></span> <span data-ttu-id="ca38c-223">Bu davranış, ' ı kaldırılarak silinebilir <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter> .</span><span class="sxs-lookup"><span data-stu-id="ca38c-223">This behavior can be deleted by removing the <xref:Microsoft.AspNetCore.Mvc.Formatters.HttpNoContentOutputFormatter>.</span></span> <span data-ttu-id="ca38c-224">Aşağıdaki kod ve öğesini kaldırır `StringOutputFormatter` `HttpNoContentOutputFormatter` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-224">The following code removes the `StringOutputFormatter` and `HttpNoContentOutputFormatter`.</span></span>

::: moniker range=">= aspnetcore-3.0"
[!code-csharp[](./formatting/3.0sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end
::: moniker range="< aspnetcore-3.0"
[!code-csharp[](./formatting/sample/StartupStringOutputFormatter.cs?name=snippet)]
::: moniker-end

<span data-ttu-id="ca38c-225">Olmadan `StringOutputFormatter` , YERLEŞIK JSON biçimlendiricisi, `string` dönüş türlerini biçimlendirir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-225">Without the `StringOutputFormatter`, the built-in JSON formatter formats `string` return types.</span></span> <span data-ttu-id="ca38c-226">Yerleşik JSON biçimlendiricisi kaldırılırsa ve bir XML biçimlendirici varsa, XML biçimlendirici, `string` dönüş türlerini biçimlendirir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-226">If the built-in JSON formatter is removed and an XML formatter is available, the XML formatter formats `string` return types.</span></span> <span data-ttu-id="ca38c-227">Aksi takdirde, `string` dönüş türleri döndürülür `406 Not Acceptable` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-227">Otherwise, `string` return types return `406 Not Acceptable`.</span></span>

<span data-ttu-id="ca38c-228">Olmadan `HttpNoContentOutputFormatter` , null nesneler yapılandırılmış biçimlendirici kullanılarak biçimlendirilir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-228">Without the `HttpNoContentOutputFormatter`, null objects are formatted using the configured formatter.</span></span> <span data-ttu-id="ca38c-229">Örnek:</span><span class="sxs-lookup"><span data-stu-id="ca38c-229">For example:</span></span>

* <span data-ttu-id="ca38c-230">JSON biçimlendiricisi, gövdesi olan bir yanıt döndürür `null` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-230">The JSON formatter returns a response with a body of `null`.</span></span>
* <span data-ttu-id="ca38c-231">XML biçimlendiricisi özniteliği ayarlanmış bir boş XML öğesi döndürüyor `xsi:nil="true"` .</span><span class="sxs-lookup"><span data-stu-id="ca38c-231">The XML formatter returns an empty XML element with the attribute `xsi:nil="true"` set.</span></span>

## <a name="response-format-url-mappings"></a><span data-ttu-id="ca38c-232">Yanıt biçimi URL eşlemeleri</span><span class="sxs-lookup"><span data-stu-id="ca38c-232">Response format URL mappings</span></span>

<span data-ttu-id="ca38c-233">İstemciler URL 'nin bir parçası olarak belirli bir biçim talep edebilir, örneğin:</span><span class="sxs-lookup"><span data-stu-id="ca38c-233">Clients can request a particular format as part of the URL, for example:</span></span>

* <span data-ttu-id="ca38c-234">Sorgu dizesinde veya yolun bir bölümünde.</span><span class="sxs-lookup"><span data-stu-id="ca38c-234">In the query string or part of the path.</span></span>
* <span data-ttu-id="ca38c-235">. Xml veya. JSON gibi formata özgü bir dosya uzantısı kullanarak.</span><span class="sxs-lookup"><span data-stu-id="ca38c-235">By using a format-specific file extension such as .xml or .json.</span></span>

<span data-ttu-id="ca38c-236">İstek yolundan eşleme, API 'nin kullandığı rotada belirtilmelidir.</span><span class="sxs-lookup"><span data-stu-id="ca38c-236">The mapping from request path should be specified in the route the API is using.</span></span> <span data-ttu-id="ca38c-237">Örnek:</span><span class="sxs-lookup"><span data-stu-id="ca38c-237">For example:</span></span>

[!code-csharp[](./formatting/sample/Controllers/ProductsController.cs?name=snippet)]

<span data-ttu-id="ca38c-238">Önceki yol, istenen biçimin isteğe bağlı bir dosya uzantısı olarak belirtilmesini sağlar.</span><span class="sxs-lookup"><span data-stu-id="ca38c-238">The preceding route allows the requested format to be specified as an optional file extension.</span></span> <span data-ttu-id="ca38c-239">[`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute)Özniteliği, içindeki biçim değerinin varlığını denetler `RouteData` ve yanıt oluşturulduğunda yanıt biçimini uygun biçimlendirici ile eşler.</span><span class="sxs-lookup"><span data-stu-id="ca38c-239">The [`[FormatFilter]`](xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute) attribute checks for the existence of the format value in the `RouteData` and maps the response format to the appropriate formatter when the response is created.</span></span>

|           <span data-ttu-id="ca38c-240">Yol</span><span class="sxs-lookup"><span data-stu-id="ca38c-240">Route</span></span>        |             <span data-ttu-id="ca38c-241">Biçimlendirici</span><span class="sxs-lookup"><span data-stu-id="ca38c-241">Formatter</span></span>              |
|---
<span data-ttu-id="ca38c-242">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-242">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-243">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-243">'Blazor'</span></span>
- <span data-ttu-id="ca38c-244">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-244">'Identity'</span></span>
- <span data-ttu-id="ca38c-245">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-245">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-246">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-246">'Razor'</span></span>
- <span data-ttu-id="ca38c-247">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-247">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-248">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-248">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-249">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-249">'Blazor'</span></span>
- <span data-ttu-id="ca38c-250">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-250">'Identity'</span></span>
- <span data-ttu-id="ca38c-251">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-251">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-252">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-252">'Razor'</span></span>
- <span data-ttu-id="ca38c-253">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-253">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-254">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-254">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-255">'Blazor'</span></span>
- <span data-ttu-id="ca38c-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-256">'Identity'</span></span>
- <span data-ttu-id="ca38c-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-258">'Razor'</span></span>
- <span data-ttu-id="ca38c-259">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-260">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-260">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-261">'Blazor'</span></span>
- <span data-ttu-id="ca38c-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-262">'Identity'</span></span>
- <span data-ttu-id="ca38c-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-264">'Razor'</span></span>
- <span data-ttu-id="ca38c-265">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-266">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-266">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-267">'Blazor'</span></span>
- <span data-ttu-id="ca38c-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-268">'Identity'</span></span>
- <span data-ttu-id="ca38c-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-270">'Razor'</span></span>
- <span data-ttu-id="ca38c-271">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-272">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-272">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-273">'Blazor'</span></span>
- <span data-ttu-id="ca38c-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-274">'Identity'</span></span>
- <span data-ttu-id="ca38c-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-276">'Razor'</span></span>
- <span data-ttu-id="ca38c-277">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-277">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-278">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-278">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-279">'Blazor'</span></span>
- <span data-ttu-id="ca38c-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-280">'Identity'</span></span>
- <span data-ttu-id="ca38c-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-282">'Razor'</span></span>
- <span data-ttu-id="ca38c-283">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-284">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-284">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-285">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-285">'Blazor'</span></span>
- <span data-ttu-id="ca38c-286">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-286">'Identity'</span></span>
- <span data-ttu-id="ca38c-287">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-287">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-288">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-288">'Razor'</span></span>
- <span data-ttu-id="ca38c-289">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-289">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-290">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-290">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-291">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-291">'Blazor'</span></span>
- <span data-ttu-id="ca38c-292">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-292">'Identity'</span></span>
- <span data-ttu-id="ca38c-293">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-293">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-294">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-294">'Razor'</span></span>
- <span data-ttu-id="ca38c-295">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-295">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-296">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-296">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-297">'Blazor'</span></span>
- <span data-ttu-id="ca38c-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-298">'Identity'</span></span>
- <span data-ttu-id="ca38c-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-300">'Razor'</span></span>
- <span data-ttu-id="ca38c-301">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-301">'SignalR' uid:</span></span> 

<span data-ttu-id="ca38c-302">------------|---title: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-302">------------|--- title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-303">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-303">'Blazor'</span></span>
- <span data-ttu-id="ca38c-304">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-304">'Identity'</span></span>
- <span data-ttu-id="ca38c-305">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-305">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-306">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-306">'Razor'</span></span>
- <span data-ttu-id="ca38c-307">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-307">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-308">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-308">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-309">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-309">'Blazor'</span></span>
- <span data-ttu-id="ca38c-310">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-310">'Identity'</span></span>
- <span data-ttu-id="ca38c-311">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-311">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-312">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-312">'Razor'</span></span>
- <span data-ttu-id="ca38c-313">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-313">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-314">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-314">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-315">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-315">'Blazor'</span></span>
- <span data-ttu-id="ca38c-316">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-316">'Identity'</span></span>
- <span data-ttu-id="ca38c-317">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-317">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-318">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-318">'Razor'</span></span>
- <span data-ttu-id="ca38c-319">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-319">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-320">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-320">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-321">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-321">'Blazor'</span></span>
- <span data-ttu-id="ca38c-322">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-322">'Identity'</span></span>
- <span data-ttu-id="ca38c-323">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-323">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-324">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-324">'Razor'</span></span>
- <span data-ttu-id="ca38c-325">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-325">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-326">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-326">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-327">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-327">'Blazor'</span></span>
- <span data-ttu-id="ca38c-328">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-328">'Identity'</span></span>
- <span data-ttu-id="ca38c-329">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-329">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-330">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-330">'Razor'</span></span>
- <span data-ttu-id="ca38c-331">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-331">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-332">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-332">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-333">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-333">'Blazor'</span></span>
- <span data-ttu-id="ca38c-334">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-334">'Identity'</span></span>
- <span data-ttu-id="ca38c-335">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-335">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-336">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-336">'Razor'</span></span>
- <span data-ttu-id="ca38c-337">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-337">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-338">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-338">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-339">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-339">'Blazor'</span></span>
- <span data-ttu-id="ca38c-340">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-340">'Identity'</span></span>
- <span data-ttu-id="ca38c-341">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-341">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-342">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-342">'Razor'</span></span>
- <span data-ttu-id="ca38c-343">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-343">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-344">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-344">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-345">'Blazor'</span></span>
- <span data-ttu-id="ca38c-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-346">'Identity'</span></span>
- <span data-ttu-id="ca38c-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-348">'Razor'</span></span>
- <span data-ttu-id="ca38c-349">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-350">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-350">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-351">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-351">'Blazor'</span></span>
- <span data-ttu-id="ca38c-352">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-352">'Identity'</span></span>
- <span data-ttu-id="ca38c-353">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-353">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-354">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-354">'Razor'</span></span>
- <span data-ttu-id="ca38c-355">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-355">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-356">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-356">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-357">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-357">'Blazor'</span></span>
- <span data-ttu-id="ca38c-358">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-358">'Identity'</span></span>
- <span data-ttu-id="ca38c-359">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-359">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-360">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-360">'Razor'</span></span>
- <span data-ttu-id="ca38c-361">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-361">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-362">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-362">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-363">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-363">'Blazor'</span></span>
- <span data-ttu-id="ca38c-364">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-364">'Identity'</span></span>
- <span data-ttu-id="ca38c-365">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-365">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-366">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-366">'Razor'</span></span>
- <span data-ttu-id="ca38c-367">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-367">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-368">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-368">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-369">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-369">'Blazor'</span></span>
- <span data-ttu-id="ca38c-370">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-370">'Identity'</span></span>
- <span data-ttu-id="ca38c-371">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-371">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-372">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-372">'Razor'</span></span>
- <span data-ttu-id="ca38c-373">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-373">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-374">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-374">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-375">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-375">'Blazor'</span></span>
- <span data-ttu-id="ca38c-376">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-376">'Identity'</span></span>
- <span data-ttu-id="ca38c-377">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-377">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-378">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-378">'Razor'</span></span>
- <span data-ttu-id="ca38c-379">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-379">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-380">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-380">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-381">'Blazor'</span></span>
- <span data-ttu-id="ca38c-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-382">'Identity'</span></span>
- <span data-ttu-id="ca38c-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-384">'Razor'</span></span>
- <span data-ttu-id="ca38c-385">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-386">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-386">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-387">'Blazor'</span></span>
- <span data-ttu-id="ca38c-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-388">'Identity'</span></span>
- <span data-ttu-id="ca38c-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-390">'Razor'</span></span>
- <span data-ttu-id="ca38c-391">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="ca38c-392">Başlık: Yazar: Açıklama: MS. Author: MS. Custom: MS. Date: No-loc:</span><span class="sxs-lookup"><span data-stu-id="ca38c-392">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="ca38c-393">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-393">'Blazor'</span></span>
- <span data-ttu-id="ca38c-394">'Identity'</span><span class="sxs-lookup"><span data-stu-id="ca38c-394">'Identity'</span></span>
- <span data-ttu-id="ca38c-395">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="ca38c-395">'Let's Encrypt'</span></span>
- <span data-ttu-id="ca38c-396">'Razor'</span><span class="sxs-lookup"><span data-stu-id="ca38c-396">'Razor'</span></span>
- <span data-ttu-id="ca38c-397">' SignalR ' uid:</span><span class="sxs-lookup"><span data-stu-id="ca38c-397">'SignalR' uid:</span></span> 

<span data-ttu-id="ca38c-398">------------------| |   `/api/products/5`    |    Varsayılan çıkış biçimlendiricisi | | `/api/products/5.json` | JSON biçimlendiricisi (yapılandırıldıysa) | | `/api/products/5.xml`  | XML biçimlendiricisi (yapılandırıldıysa) |</span><span class="sxs-lookup"><span data-stu-id="ca38c-398">------------------| |   `/api/products/5`    |    The default output formatter    | | `/api/products/5.json` | The JSON formatter (if configured) | | `/api/products/5.xml`  | The XML formatter (if configured)  |</span></span>
