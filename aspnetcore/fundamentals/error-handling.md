---
title: ASP.NET Core'daki hataları işleme
author: rick-anderson
description: ASP.NET Core uygulamalarındaki hataları nasıl işleyeceğinizi keşfedin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 28b463bccfb8aff4d10b95aa9a984455b4f4b976
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658818"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="90082-103">ASP.NET Core'daki hataları işleme</span><span class="sxs-lookup"><span data-stu-id="90082-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="90082-104">Yazar: [Tom Dykstra](https://github.com/tdykstra/) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="90082-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="90082-105">Bu makalede, ASP.NET Core web uygulamalarında işleme hataları için ortak yaklaşımlar kapsar.</span><span class="sxs-lookup"><span data-stu-id="90082-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="90082-106">Web <xref:web-api/handle-errors> API'leri için bkz.</span><span class="sxs-lookup"><span data-stu-id="90082-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="90082-107">[Örnek kodu görüntüleyin veya indirin.](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)</span><span class="sxs-lookup"><span data-stu-id="90082-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="90082-108">([Nasıl indirilir](xref:index#how-to-download-a-sample).) Makale, farklı senaryoları etkinleştirmek için`#if`örnek `#endif` `#define`uygulamada önişlemci yönergelerinin (, , ) nasıl ayarlanacaklarına ilişkin yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="90082-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="90082-109">Geliştirici Özel Durum Sayfası</span><span class="sxs-lookup"><span data-stu-id="90082-109">Developer Exception Page</span></span>

<span data-ttu-id="90082-110">*Geliştirici Özel Durum Sayfası,* istek özel durumları hakkında ayrıntılı bilgiler görüntüler.</span><span class="sxs-lookup"><span data-stu-id="90082-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="90082-111">Sayfa [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi tarafından kullanılabilir hale getirilir, Hangi [Microsoft.AspNetCore.App metapackage.](xref:fundamentals/metapackage-app)</span><span class="sxs-lookup"><span data-stu-id="90082-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="90082-112">Uygulama Geliştirme `Startup.Configure` [ortamında](xref:fundamentals/environments)çalışırken sayfayı etkinleştirmek için yönteme kod ekleyin:</span><span class="sxs-lookup"><span data-stu-id="90082-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="90082-113">Aramayı, özel <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> durumları yakalamak istediğiniz herhangi bir ara yazılımdan önce yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="90082-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="90082-114">Geliştirici Özel Durum Sayfasını **yalnızca uygulama Geliştirme ortamında çalışırken**etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="90082-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="90082-115">Uygulama üretimde çalışırken ayrıntılı özel durum bilgilerini genel olarak paylaşmak istemezsinuz.</span><span class="sxs-lookup"><span data-stu-id="90082-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="90082-116">Ortamları yapılandırma hakkında daha <xref:fundamentals/environments>fazla bilgi için bkz.</span><span class="sxs-lookup"><span data-stu-id="90082-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="90082-117">Sayfa, özel durum ve istek le ilgili aşağıdaki bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="90082-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="90082-118">Yığın izi</span><span class="sxs-lookup"><span data-stu-id="90082-118">Stack trace</span></span>
* <span data-ttu-id="90082-119">Sorgu dize parametreleri (varsa)</span><span class="sxs-lookup"><span data-stu-id="90082-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="90082-120">Çerezler (varsa)</span><span class="sxs-lookup"><span data-stu-id="90082-120">Cookies (if any)</span></span>
* <span data-ttu-id="90082-121">Üst bilgiler</span><span class="sxs-lookup"><span data-stu-id="90082-121">Headers</span></span>

<span data-ttu-id="90082-122">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)Geliştirici Özel Durum Sayfasını görmek `DevEnvironment` için önişlemci yönergesini kullanın ve ana sayfada **bir özel durum tetikle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="90082-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="90082-123">Özel durum işleyicisi sayfası</span><span class="sxs-lookup"><span data-stu-id="90082-123">Exception handler page</span></span>

<span data-ttu-id="90082-124">Üretim ortamı için özel hata işleme sayfasını yapılandırmak için Özel Durum İşleme Aracı'nı kullanın.</span><span class="sxs-lookup"><span data-stu-id="90082-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="90082-125">Ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="90082-125">The middleware:</span></span>

* <span data-ttu-id="90082-126">Özel durumları yakalar ve günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="90082-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="90082-127">Belirtilen sayfa veya denetleyici için alternatif bir ardışık ardışık durumda isteği yeniden yürütür.</span><span class="sxs-lookup"><span data-stu-id="90082-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="90082-128">Yanıt başlatılırsa istek yeniden yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="90082-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="90082-129">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> Geliştirme dışı ortamlarda Özel Durum İşleme Aracı'nı ekler:</span><span class="sxs-lookup"><span data-stu-id="90082-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="90082-130">Razor Pages uygulama şablonu, *Sayfalar* klasöründe bir Hata`ErrorModel`sayfası (*.cshtml*) ve <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> sınıf ( ) sağlar.</span><span class="sxs-lookup"><span data-stu-id="90082-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="90082-131">Bir MVC uygulaması için proje şablonu bir Hata eylem yöntemi ve hata görünümü içerir.</span><span class="sxs-lookup"><span data-stu-id="90082-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="90082-132">Eylem yöntemi aşağıda veda eder:</span><span class="sxs-lookup"><span data-stu-id="90082-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="90082-133">Hata işleyicisi eylem yöntemini HTTP yöntemi öznitelikleriyle `HttpGet`işaretlemeyin.</span><span class="sxs-lookup"><span data-stu-id="90082-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="90082-134">Açık fiiller bazı isteklerin yönteme ulaşmasını engeller.</span><span class="sxs-lookup"><span data-stu-id="90082-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="90082-135">Kimliği doğrulanmamış kullanıcıların hata görünümünü alabilmeleri için yönteme anonim erişime izin verin.</span><span class="sxs-lookup"><span data-stu-id="90082-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="90082-136">Özel durum erişin</span><span class="sxs-lookup"><span data-stu-id="90082-136">Access the exception</span></span>

<span data-ttu-id="90082-137">Hata <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> işleyicidenetleyicisi veya sayfasındaki özel durum ve özgün istek yoluna erişmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="90082-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="90082-138">Hassas hata bilgilerini istemcilere **sunmayın.**</span><span class="sxs-lookup"><span data-stu-id="90082-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="90082-139">Hizmet hataları bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="90082-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="90082-140">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)özel durum işleme sayfasını görmek `ProdEnvironment` `ErrorHandlerPage` için, ve önişlemci yönergelerini kullanın ve ana sayfada **bir özel durum tetikle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="90082-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="90082-141">İstisna işleyicisi lambda</span><span class="sxs-lookup"><span data-stu-id="90082-141">Exception handler lambda</span></span>

<span data-ttu-id="90082-142">Özel bir [özel durum işleyicisi sayfasına](#exception-handler-page) <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>alternatif olarak bir lambda sağlamaktır.</span><span class="sxs-lookup"><span data-stu-id="90082-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="90082-143">Lambda kullanmak yanıtı döndürmeden önce hataya erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="90082-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="90082-144">Aşağıda, özel durum kullanımı için lambda kullanmanın bir örneği verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="90082-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="90082-145">Önceki kodda, `await context.Response.WriteAsync(new string(' ', 512));` Internet Explorer tarayıcısının IE hata iletisi yerine hata iletisini görüntülemesi için eklenir.</span><span class="sxs-lookup"><span data-stu-id="90082-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="90082-146">Daha fazla bilgi için [bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16144)bakın.</span><span class="sxs-lookup"><span data-stu-id="90082-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="90082-147">Müşterilerden veya <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> istemcilerden <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> hassas hata bilgileri **sunmayın.**</span><span class="sxs-lookup"><span data-stu-id="90082-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="90082-148">Hizmet hataları bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="90082-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="90082-149">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)lambda'yı kullanma özel durum sonucunu `ProdEnvironment` görmek `ErrorHandlerLambda` için, ve önişlemci yönergelerini kullanın ve ana sayfada **bir özel durum tetikle'yi** seçin.</span><span class="sxs-lookup"><span data-stu-id="90082-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="90082-150">StatusCodePages'i Kullanma</span><span class="sxs-lookup"><span data-stu-id="90082-150">UseStatusCodePages</span></span>

<span data-ttu-id="90082-151">Varsayılan olarak, bir ASP.NET Core uygulaması *404 - Bulunamadı*gibi HTTP durum kodları için bir durum kodu sayfası sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="90082-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="90082-152">Uygulama bir durum kodu ve boş bir yanıt gövdesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="90082-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="90082-153">Durum kodu sayfalarını sağlamak için Durum Kodu Sayfaları ara yazılımlarını kullanın.</span><span class="sxs-lookup"><span data-stu-id="90082-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="90082-154">Middleware [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi, [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)olan tarafından kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="90082-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="90082-155">Yaygın hata durum kodları için varsayılan metin yalnızca <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> işleyicileri etkinleştirmek `Startup.Configure` için, yöntemde arayın:</span><span class="sxs-lookup"><span data-stu-id="90082-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="90082-156">Ara `UseStatusCodePages` yazılım işleme isteği önce arayın (örneğin, Statik Dosya Middleware ve MVC Middleware).</span><span class="sxs-lookup"><span data-stu-id="90082-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="90082-157">Varsayılan işleyiciler tarafından görüntülenen metnin bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="90082-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="90082-158">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)çeşitli durum kodu sayfa biçimlerinden birini görmek `StatusCodePages`için, başlangıç olan önişlemci yönergelerinden birini kullanın ve ana sayfada **404** Tetikle'yi seçin.</span><span class="sxs-lookup"><span data-stu-id="90082-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="90082-159">Biçim dizesi ile StatusCodePages'i kullanma</span><span class="sxs-lookup"><span data-stu-id="90082-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="90082-160">Yanıt içeriği türünü ve metnini özelleştirmek için, içerik türü ve biçim dizesini <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> alan aşırı yükkullanın:</span><span class="sxs-lookup"><span data-stu-id="90082-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="90082-161">Lambda ile StatusCodePages'i Kullanma</span><span class="sxs-lookup"><span data-stu-id="90082-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="90082-162">Özel hata işleme ve yanıt yazma kodu belirtmek için, bir lambda ifadesi alır aşırı yük <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> kullanın:</span><span class="sxs-lookup"><span data-stu-id="90082-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="90082-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="90082-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="90082-164">Uzatma <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> yöntemi:</span><span class="sxs-lookup"><span data-stu-id="90082-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="90082-165">İstemciye *302 - Bulunan* durum kodunu gönderir.</span><span class="sxs-lookup"><span data-stu-id="90082-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="90082-166">İstemciyi URL şablonunda sağlanan konuma yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="90082-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="90082-167">URL şablonu, `{0}` örnekte gösterildiği gibi durum kodu için bir yer tutucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="90082-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="90082-168">URL şablonu bir tilde (~) ile başlarsa, tilde uygulamanın `PathBase`.</span><span class="sxs-lookup"><span data-stu-id="90082-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="90082-169">Uygulama içinde bir bitiş noktası işaret ederseniz, bitiş noktası için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="90082-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="90082-170">Jilet Sayfaları örneği için [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode.cshtml* 'e bakın.</span><span class="sxs-lookup"><span data-stu-id="90082-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="90082-171">Bu yöntem genellikle uygulama aşağıdaki nde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="90082-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="90082-172">İstemciyi genellikle farklı bir uygulamanın hatayı işlediği durumlarda farklı bir bitiş noktasına yönlendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="90082-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="90082-173">Web uygulamaları için istemcinin tarayıcı adresi çubuğu yeniden yönlendirilen bitiş noktasını yansıtır.</span><span class="sxs-lookup"><span data-stu-id="90082-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="90082-174">Özgün durum kodunu ilk yönlendirme yanıtıyla korumamalı ve döndürmemelidir.</span><span class="sxs-lookup"><span data-stu-id="90082-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="90082-175">UseStatusCodePagesWithreexecute</span><span class="sxs-lookup"><span data-stu-id="90082-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="90082-176">Uzatma <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> yöntemi:</span><span class="sxs-lookup"><span data-stu-id="90082-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="90082-177">Özgün durum kodunu istemciye verir.</span><span class="sxs-lookup"><span data-stu-id="90082-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="90082-178">Alternatif bir yol kullanarak istek ardışık hattını yeniden çalıştırarak yanıt gövdesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="90082-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="90082-179">Uygulama içinde bir bitiş noktası işaret ederseniz, bitiş noktası için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="90082-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="90082-180">Jilet Sayfaları örneği için [örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode.cshtml* 'e bakın.</span><span class="sxs-lookup"><span data-stu-id="90082-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="90082-181">Bu yöntem genellikle uygulama nın aşağıdakileri yapması gerektiğinde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="90082-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="90082-182">İsteği farklı bir bitiş noktasına yönlendirmeden işle.</span><span class="sxs-lookup"><span data-stu-id="90082-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="90082-183">Web uygulamaları için, istemcinin tarayıcı adresi çubuğu başlangıçta istenen bitiş noktasını yansıtır.</span><span class="sxs-lookup"><span data-stu-id="90082-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="90082-184">Yanıtla birlikte özgün durum kodunu koruyun ve döndürün.</span><span class="sxs-lookup"><span data-stu-id="90082-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="90082-185">URL ve sorgu dize şablonları durum`{0}`kodu için bir yer tutucu ( ) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="90082-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="90082-186">URL şablonu bir eğik`/`çizgi ile başlamalıdır ( ).</span><span class="sxs-lookup"><span data-stu-id="90082-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="90082-187">Yolda bir yer tutucu kullanırken, bitiş noktasının (sayfa veya denetleyicinin) yol kesimini işleyebileceğini onaylayın.</span><span class="sxs-lookup"><span data-stu-id="90082-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="90082-188">Örneğin, hatalar için bir Razor Page `@page` yönergesi ile isteğe bağlı yol segment değeri kabul etmelidir:</span><span class="sxs-lookup"><span data-stu-id="90082-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="90082-189">Hatayı işleyen bitiş noktası, aşağıdaki örnekte gösterildiği gibi hatayı oluşturan özgün URL'yi alabilir:</span><span class="sxs-lookup"><span data-stu-id="90082-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="90082-190">Durum kodu sayfalarını devre dışı bırakma</span><span class="sxs-lookup"><span data-stu-id="90082-190">Disable status code pages</span></span>

<span data-ttu-id="90082-191">MVC denetleyicisi veya eylem yöntemi için durum kodu [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) sayfalarını devre dışı kullanabilirsiniz, özniteliği kullanın.</span><span class="sxs-lookup"><span data-stu-id="90082-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="90082-192">Razor Pages işleyicisi yönteminde veya Bir MVC denetleyicisinde belirli istekler için durum kodu sayfalarını devre dışı kullanabilirsiniz: <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature></span><span class="sxs-lookup"><span data-stu-id="90082-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="90082-193">Özel durum işleme kodu</span><span class="sxs-lookup"><span data-stu-id="90082-193">Exception-handling code</span></span>

<span data-ttu-id="90082-194">Özel durum işleme sayfalarındaki kod özel durumlar atabilir.</span><span class="sxs-lookup"><span data-stu-id="90082-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="90082-195">Üretim hatası sayfalarının tamamen statik içerikten oluşması genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="90082-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="90082-196">Yanıt üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="90082-196">Response headers</span></span>

<span data-ttu-id="90082-197">Yanıt için üstbilgi gönderildikten sonra:</span><span class="sxs-lookup"><span data-stu-id="90082-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="90082-198">Uygulama yanıtın durum kodunu değiştiremez.</span><span class="sxs-lookup"><span data-stu-id="90082-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="90082-199">Herhangi bir özel durum sayfaları veya işleyicileri çalıştıramaz.</span><span class="sxs-lookup"><span data-stu-id="90082-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="90082-200">Yanıt tamamlanmalı veya bağlantı iptal edilmelidir.</span><span class="sxs-lookup"><span data-stu-id="90082-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="90082-201">Sunucu özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="90082-201">Server exception handling</span></span>

<span data-ttu-id="90082-202">Uygulamanızdaki özel durum işleme mantığına ek olarak, [HTTP sunucu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="90082-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="90082-203">Yanıt üstbilgisi gönderilmeden önce sunucu bir özel durum yakalarsa, yanıt gövdesi olmadan sunucu *500 - Internal Server Error* yanıtı gönderir.</span><span class="sxs-lookup"><span data-stu-id="90082-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="90082-204">Yanıt üstbilgisi gönderildikten sonra sunucu bir özel durum yakalarsa, sunucu bağlantıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="90082-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="90082-205">Uygulamanız tarafından işlenmemiş istekler sunucu tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="90082-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="90082-206">Sunucu isteği işlerken oluşan herhangi bir özel durum, sunucunun özel durum işleme tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="90082-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="90082-207">Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımve filtreler bu davranışı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="90082-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="90082-208">Başlangıç özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="90082-208">Startup exception handling</span></span>

<span data-ttu-id="90082-209">Yalnızca barındırma katmanı, uygulama nın başlatılması sırasında gerçekleşen özel durumları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="90082-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="90082-210">Ana [bilgisayar, başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="90082-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="90082-211">Barındırma katmanı, yakalanan bir başlangıç hatası için yalnızca ana bilgisayar adresi/bağlantı noktası bağlama sonrasında hata oluşursa bir hata sayfası gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="90082-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="90082-212">Bağlama başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="90082-212">If binding fails:</span></span>

* <span data-ttu-id="90082-213">Barındırma katmanı kritik bir özel durum günlükleri.</span><span class="sxs-lookup"><span data-stu-id="90082-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="90082-214">Dotnet işlemi çöküyor.</span><span class="sxs-lookup"><span data-stu-id="90082-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="90082-215">HTTP sunucusu [Kerkenez](xref:fundamentals/servers/kestrel)olduğunda hiçbir hata sayfası görüntülenmez.</span><span class="sxs-lookup"><span data-stu-id="90082-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="90082-216">[IIS](/iis) (veya Azure Uygulama Hizmeti) veya [IIS Express'te](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, *502,5 - İşlem Hatası,* işlem başlatılamazsa [ASP.NET Çekirdek Modülü](xref:host-and-deploy/aspnet-core-module) tarafından döndürülür.</span><span class="sxs-lookup"><span data-stu-id="90082-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="90082-217">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="90082-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="90082-218">Veritabanı hata sayfası</span><span class="sxs-lookup"><span data-stu-id="90082-218">Database error page</span></span>

<span data-ttu-id="90082-219">Veritabanı Hata Sayfası Middleware, Entity Framework geçişlerini kullanarak çözülebilen veritabanıyla ilgili özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="90082-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="90082-220">Bu özel durumlar oluştuğunda, sorunu gidermek için olası eylemlerin ayrıntılarını içeren bir HTML yanıtı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="90082-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="90082-221">Bu sayfa yalnızca Geliştirme ortamında etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="90082-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="90082-222">`Startup.Configure`Aşağıdakilere kod ekleyerek sayfayı etkinleştirin:</span><span class="sxs-lookup"><span data-stu-id="90082-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="90082-223">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="90082-223">Exception filters</span></span>

<span data-ttu-id="90082-224">MVC uygulamalarında, özel durum filtreleri genel olarak veya denetleyici başına veya eylem başına olarak yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="90082-224">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="90082-225">Razor Pages uygulamalarında, bunlar genel olarak veya sayfa modeli başına yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="90082-225">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="90082-226">Bu filtreler, denetleyici eylemi veya başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumu işler.</span><span class="sxs-lookup"><span data-stu-id="90082-226">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="90082-227">Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="90082-227">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="90082-228">Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için yararlıdır, ancak Özel Durum İşleme Aracı kadar esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="90082-228">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="90082-229">Ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="90082-229">We recommend using the middleware.</span></span> <span data-ttu-id="90082-230">Filtreleri yalnızca mvc eyleminin seçildiğine göre farklı hata işleme gerçekleştirmeniz gereken yerlerde kullanın.</span><span class="sxs-lookup"><span data-stu-id="90082-230">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="90082-231">Model durum hataları</span><span class="sxs-lookup"><span data-stu-id="90082-231">Model state errors</span></span>

<span data-ttu-id="90082-232">Model durumu hatalarının nasıl işleyeceğiniz hakkında bilgi için [Model bağlama](xref:mvc/models/model-binding) ve [Model doğrulamasına](xref:mvc/models/validation)bakın.</span><span class="sxs-lookup"><span data-stu-id="90082-232">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="90082-233">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="90082-233">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
