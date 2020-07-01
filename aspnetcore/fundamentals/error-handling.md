---
title: ASP.NET Core hataları işleme
author: rick-anderson
description: ASP.NET Core uygulamalarında hataların nasıl işleneceğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/error-handling
ms.openlocfilehash: 857b4935816d0010fa425ce98f938c87eb35a581
ms.sourcegitcommit: 895e952aec11c91d703fbdd3640a979307b8cc67
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/01/2020
ms.locfileid: "85793447"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="3c0e0-103">ASP.NET Core hataları işleme</span><span class="sxs-lookup"><span data-stu-id="3c0e0-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="3c0e0-104">[Tom Dykstra](https://github.com/tdykstra/) ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="3c0e0-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3c0e0-105">Bu makalede ASP.NET Core Web Apps 'teki hataları işlemeye yönelik yaygın yaklaşımlar ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="3c0e0-106">Bkz <xref:web-api/handle-errors> . Web API 'leri için.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="3c0e0-107">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="3c0e0-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="3c0e0-108">([Nasıl indirilir](xref:index#how-to-download-a-sample).) Makale, `#if` `#endif` `#define` farklı senaryolara olanak tanımak için örnek uygulamada Önişlemci yönergelerinin (,,) nasıl ayarlanacağı hakkında yönergeler içerir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="3c0e0-109">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="3c0e0-109">Developer Exception Page</span></span>

<span data-ttu-id="3c0e0-110">*Geliştirici özel durum sayfası* , istek özel durumları hakkında ayrıntılı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="3c0e0-111">Sayfa, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içindeki [Microsoft. Aspnetcore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi tarafından kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="3c0e0-112">`Startup.Configure`Uygulama geliştirme [ortamında](xref:fundamentals/environments)çalışırken sayfayı etkinleştirmek için yöntemine kod ekleyin:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="3c0e0-113"><xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>Özel durumları yakalamak istediğiniz herhangi bir ara yazılım için çağrıyı buraya yerleştirin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="3c0e0-114">Geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken**etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="3c0e0-115">Uygulama üretimde çalıştırıldığında ayrıntılı özel durum bilgilerini herkese açık bir şekilde paylaşmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="3c0e0-116">Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="3c0e0-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="3c0e0-117">Sayfa, özel durum ve istek hakkında şu bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="3c0e0-118">Yığın izleme</span><span class="sxs-lookup"><span data-stu-id="3c0e0-118">Stack trace</span></span>
* <span data-ttu-id="3c0e0-119">Sorgu dizesi parametreleri (varsa)</span><span class="sxs-lookup"><span data-stu-id="3c0e0-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="3c0e0-120">Tanımlama bilgileri (varsa)</span><span class="sxs-lookup"><span data-stu-id="3c0e0-120">Cookies (if any)</span></span>
* <span data-ttu-id="3c0e0-121">Üst Bilgiler</span><span class="sxs-lookup"><span data-stu-id="3c0e0-121">Headers</span></span>

<span data-ttu-id="3c0e0-122">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)geliştirici özel durum sayfasını görmek için, ön `DevEnvironment` işlemci yönergesini kullanın ve giriş sayfasında **özel durum Tetikle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="3c0e0-123">Özel durum işleyici sayfası</span><span class="sxs-lookup"><span data-stu-id="3c0e0-123">Exception handler page</span></span>

<span data-ttu-id="3c0e0-124">Üretim ortamı için özel bir hata işleme sayfası yapılandırmak için özel durum Işleme ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="3c0e0-125">Ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-125">The middleware:</span></span>

* <span data-ttu-id="3c0e0-126">Özel durumları yakalar ve günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="3c0e0-127">İsteği, belirtilen sayfa veya denetleyici için alternatif bir ardışık düzende yeniden yürütür.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="3c0e0-128">Yanıt başlatılmışsa istek yeniden yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="3c0e0-129">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> özel durum Işleme ara yazılımını geliştirme olmayan ortamlara ekler:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="3c0e0-130">RazorSayfalar uygulama şablonu, sayfalar klasöründe bir hata sayfası (*. cshtml*) ve <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Sınıf ( `ErrorModel` ) sağlar *Pages* .</span><span class="sxs-lookup"><span data-stu-id="3c0e0-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="3c0e0-131">MVC uygulaması için, proje şablonu bir hata eylemi yöntemi ve bir hata görünümü içerir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="3c0e0-132">Eylem yöntemi aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="3c0e0-133">Hata işleyicisi eylem yöntemini, gibi HTTP yöntemi öznitelikleriyle işaretlemeyin `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="3c0e0-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="3c0e0-134">Açık fiiller bazı isteklerin yönteme ulaşmasını önler.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="3c0e0-135">Kimliği doğrulanmamış kullanıcıların hata görünümünü alabilmesi için metoda anonim erişime izin verin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="3c0e0-136">Özel duruma erişin</span><span class="sxs-lookup"><span data-stu-id="3c0e0-136">Access the exception</span></span>

<span data-ttu-id="3c0e0-137"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>Hata işleyicisi denetleyicisi veya sayfasındaki özel duruma ve özgün istek yoluna erişmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="3c0e0-138">İstemcilere **not** hassas hata bilgileri sunma.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="3c0e0-139">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="3c0e0-140">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)özel durum işleme sayfasını görmek için, `ProdEnvironment` ve ön `ErrorHandlerPage` işlemci yönergelerini kullanın ve giriş sayfasında **özel durum Tetikle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="3c0e0-141">Özel durum işleyici lambda</span><span class="sxs-lookup"><span data-stu-id="3c0e0-141">Exception handler lambda</span></span>

<span data-ttu-id="3c0e0-142">Özel bir özel [durum işleyici sayfasına](#exception-handler-page) bir alternatif, için bir lambda sağlamaktır <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> .</span><span class="sxs-lookup"><span data-stu-id="3c0e0-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="3c0e0-143">Lambda kullanılması, yanıtı döndürmeden önce hataya erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="3c0e0-144">Özel durum işleme için lambda kullanmanın bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="3c0e0-145">Yukarıdaki kodda, `await context.Response.WriteAsync(new string(' ', 512));` Internet Explorer tarayıcısı BIR IE hata iletisi yerine hata iletisini görüntüleyecek şekilde eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="3c0e0-146">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16144)bakın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="3c0e0-147">Ya **not** da istemcilerinden önemli hata bilgileri sunma <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> .</span><span class="sxs-lookup"><span data-stu-id="3c0e0-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="3c0e0-148">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="3c0e0-149">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)lambda işlemenin özel durum işleme sonucunu görmek için, `ProdEnvironment` ve ön `ErrorHandlerLambda` işlemci yönergelerini kullanın ve giriş sayfasında **özel durum Tetikle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="3c0e0-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="3c0e0-150">UseStatusCodePages</span></span>

<span data-ttu-id="3c0e0-151">Varsayılan olarak, bir ASP.NET Core uygulama HTTP durum kodları için *404-bulunamadı*gibi bir durum kodu sayfası sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="3c0e0-152">Uygulama bir durum kodu ve boş bir yanıt gövdesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="3c0e0-153">Durum kodu sayfaları sağlamak için durum kodu sayfaları ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="3c0e0-154">Ara yazılım, [Microsoft. aspnetcore. app metapackage](xref:fundamentals/metapackage-app)içindeki [Microsoft. Aspnetcore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi tarafından kullanılabilir hale gelir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3c0e0-155">Ortak hata durum kodları için varsayılan salt metin işleyicilerini etkinleştirmek üzere <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> `Startup.Configure` yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="3c0e0-156">`UseStatusCodePages`İstek işleme ara yazılımı (örneğin, statik dosya ara yazılımı ve MVC ara yazılımı) önce çağırın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="3c0e0-157">Varsayılan işleyiciler tarafından görüntülenbir metin örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="3c0e0-158">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)çeşitli durum kodu sayfası biçimlerinden birini görmek için, ile başlayan Önişlemci yönergelerinden birini kullanın `StatusCodePages` ve giriş sayfasında **404 tetikleme** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="3c0e0-159">Biçim dizesiyle UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="3c0e0-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="3c0e0-160">Yanıt içerik türünü ve metnini özelleştirmek için, öğesinin <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> bir içerik türü ve biçim dizesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="3c0e0-161">Lambda ile UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="3c0e0-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="3c0e0-162">Özel hata işleme ve yanıt yazma kodu belirtmek için, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> bir lambda ifadesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="3c0e0-163">Usestatuscodepageswithyönlendirmeler</span><span class="sxs-lookup"><span data-stu-id="3c0e0-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="3c0e0-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="3c0e0-165">İstemciye *302 tarafından bulunan* bir durum kodu gönderir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="3c0e0-166">İstemciyi, URL şablonunda belirtilen konuma yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="3c0e0-167">URL şablonu, `{0}` örnekte gösterildiği gibi durum kodu için bir yer tutucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="3c0e0-168">URL şablonu bir tilde (~) ile başlıyorsa, tilde uygulama ile değiştirilmiştir `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="3c0e0-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="3c0e0-169">Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="3c0e0-170">Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="3c0e0-171">Bu yöntem genellikle uygulama şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="3c0e0-172">, Genellikle farklı bir uygulamanın hatayı işlediği durumlarda istemciyi farklı bir uç noktaya yönlendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="3c0e0-173">Web Apps için, istemcinin tarayıcı adres çubuğu yeniden yönlendirilen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="3c0e0-174">İlk yeniden yönlendirme yanıtıyla birlikte özgün durum kodunu korumamalıdır ve döndürmemelidir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="3c0e0-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="3c0e0-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="3c0e0-176"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="3c0e0-177">İstemciye özgün durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="3c0e0-178">Alternatif bir yol kullanarak istek ardışık düzenini yeniden yürüterek yanıt gövdesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="3c0e0-179">Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="3c0e0-180">Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-180">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="3c0e0-181">Bu yöntem genellikle uygulama şunları yaparken kullanılır:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-181">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="3c0e0-182">Farklı bir uç noktaya yönlendirmeye gerek kalmadan isteği işleyin.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-182">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="3c0e0-183">Web Apps için, istemcinin tarayıcı adres çubuğu, ilk olarak istenen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-183">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="3c0e0-184">Yanıt ile orijinal durum kodunu koruyun ve geri döndürün.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-184">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="3c0e0-185">URL ve sorgu dizesi şablonları durum kodu için bir yer tutucu ( `{0}` ) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-185">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="3c0e0-186">URL şablonu eğik çizgiyle ( `/` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-186">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="3c0e0-187">Yolda bir yer tutucu kullanırken, uç noktanın (sayfa veya denetleyicinin) yol kesimini işleyediğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-187">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="3c0e0-188">Örneğin, bir Razor sayfa, yönergeyle isteğe bağlı yol segmenti değerini kabul etmelidir `@page` :</span><span class="sxs-lookup"><span data-stu-id="3c0e0-188">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="3c0e0-189">Hatayı işleyen uç nokta, aşağıdaki örnekte gösterildiği gibi, hatayı oluşturan özgün URL 'YI alabilir:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-189">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="3c0e0-190">Durum kodu sayfalarını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="3c0e0-190">Disable status code pages</span></span>

<span data-ttu-id="3c0e0-191">MVC denetleyicisi veya eylem yöntemi için durum kodu sayfalarını devre dışı bırakmak için özniteliğini kullanın [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="3c0e0-191">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="3c0e0-192">Bir Razor sayfa işleyici yöntemindeki veya BIR MVC denetleyicisindeki belirli istekler için durum kodu sayfalarını devre dışı bırakmak için şunu kullanın <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="3c0e0-192">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="3c0e0-193">Özel durum işleme kodu</span><span class="sxs-lookup"><span data-stu-id="3c0e0-193">Exception-handling code</span></span>

<span data-ttu-id="3c0e0-194">Özel durum işleme sayfalarındaki kod özel durumlar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-194">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="3c0e0-195">Üretim hatası sayfalarının tamamen statik içerikten oluşması genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-195">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="3c0e0-196">Yanıt üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="3c0e0-196">Response headers</span></span>

<span data-ttu-id="3c0e0-197">Yanıt üstbilgileri gönderildikten sonra:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-197">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="3c0e0-198">Uygulama, yanıtın durum kodunu değiştiremiyor.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-198">The app can't change the response's status code.</span></span>
* <span data-ttu-id="3c0e0-199">Tüm özel durum sayfaları veya işleyicileri çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-199">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="3c0e0-200">Yanıt tamamlanmalıdır veya bağlantı iptal edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-200">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="3c0e0-201">Sunucu özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="3c0e0-201">Server exception handling</span></span>

<span data-ttu-id="3c0e0-202">Uygulamanızın özel durum işleme mantığına ek olarak, [http sunucusu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-202">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="3c0e0-203">Sunucu yanıt üstbilgileri gönderilmeden önce bir özel durum yakalarsa, sunucu yanıt gövdesi olmadan *500-Iç sunucu hatası* yanıtı gönderir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-203">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="3c0e0-204">Yanıt üstbilgileri gönderildikten sonra sunucu bir özel durum yakalar, sunucu bağlantıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-204">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="3c0e0-205">Uygulamanız tarafından işlenmeyen istekler sunucu tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-205">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="3c0e0-206">Sunucu isteği gerçekleştirirken oluşan tüm özel durumlar sunucunun özel durum işleme tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-206">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="3c0e0-207">Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımı ve filtreler bu davranışı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-207">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="3c0e0-208">Başlatma özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="3c0e0-208">Startup exception handling</span></span>

<span data-ttu-id="3c0e0-209">Uygulama başlangıcında gerçekleşen özel durumları yalnızca barındırma katmanı işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-209">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="3c0e0-210">Konak, [Başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-210">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="3c0e0-211">Barındırma katmanı, yalnızca hatanın ana bilgisayar adresi/bağlantı noktası bağlamalarından sonra oluşması durumunda yakalanan başlatma hatası için bir hata sayfası gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-211">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="3c0e0-212">Bağlama başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="3c0e0-212">If binding fails:</span></span>

* <span data-ttu-id="3c0e0-213">Barındırma katmanı, kritik bir özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-213">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="3c0e0-214">DotNet işlemi kilitleniyor.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-214">The dotnet process crashes.</span></span>
* <span data-ttu-id="3c0e0-215">HTTP sunucusu [Kestrel](xref:fundamentals/servers/kestrel)olduğunda hata sayfası gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-215">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="3c0e0-216">[IIS](/iis) 'de (veya Azure App Service) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, işlem başlatılamazsa [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) tarafından *502,5 işlem hatası* döndürülür.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-216">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="3c0e0-217">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-217">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="3c0e0-218">Veritabanı hata sayfası</span><span class="sxs-lookup"><span data-stu-id="3c0e0-218">Database error page</span></span>

<span data-ttu-id="3c0e0-219">Veritabanı hata sayfası ara yazılımı, Entity Framework geçişleri kullanılarak çözümleneyolabilecek veritabanıyla ilgili özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-219">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="3c0e0-220">Bu özel durumlar gerçekleştiğinde, sorunu çözmeye yönelik olası eylemlerin ayrıntılarını içeren bir HTML yanıtı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-220">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="3c0e0-221">Bu sayfa yalnızca geliştirme ortamında etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-221">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="3c0e0-222">Aşağıdakileri kod ekleyerek sayfayı etkinleştirin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="3c0e0-222">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="3c0e0-223"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>[Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-223"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="3c0e0-224">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="3c0e0-224">Exception filters</span></span>

<span data-ttu-id="3c0e0-225">MVC uygulamalarında özel durum filtreleri, genel olarak veya denetleyicide veya eylem temelinde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-225">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="3c0e0-226">RazorSayfalar uygulamalarda, genel olarak veya sayfa modeli başına yapılandırılabilirler.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-226">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="3c0e0-227">Bu filtreler, bir denetleyici eyleminin veya başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumları işler.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-227">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="3c0e0-228">Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-228">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="3c0e0-229">Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için yararlıdır, ancak özel durum Işleme ara yazılımı kadar esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-229">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="3c0e0-230">Ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-230">We recommend using the middleware.</span></span> <span data-ttu-id="3c0e0-231">Yalnızca hangi MVC eyleminin seçilmesinden ayrı olarak hata işleme yapmanız gereken durumlarda filtreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="3c0e0-231">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="3c0e0-232">Model durumu hataları</span><span class="sxs-lookup"><span data-stu-id="3c0e0-232">Model state errors</span></span>

<span data-ttu-id="3c0e0-233">Model durumu hatalarını işleme hakkında daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) ve [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="3c0e0-233">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3c0e0-234">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="3c0e0-234">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
