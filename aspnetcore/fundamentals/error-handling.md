---
title: ASP.NET Core hataları işleme
author: rick-anderson
description: ASP.NET Core uygulamalarında hataların nasıl işleneceğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/error-handling
ms.openlocfilehash: c8174c7e253a596d02dbc6cec183453b3723bc24
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060475"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="0821d-103">ASP.NET Core hataları işleme</span><span class="sxs-lookup"><span data-stu-id="0821d-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="0821d-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="0821d-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0821d-105">Bu makalede ASP.NET Core Web Apps 'teki hataları işlemeye yönelik yaygın yaklaşımlar ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0821d-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="0821d-106">Bkz <xref:web-api/handle-errors> . Web API 'leri için.</span><span class="sxs-lookup"><span data-stu-id="0821d-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="0821d-107">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="0821d-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="0821d-108">([Nasıl indirilir](xref:index#how-to-download-a-sample).) F12 tarayıcı geliştirici araçlarındaki ağ sekmesi, örnek uygulamayı test ederken faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="0821d-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="0821d-109">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="0821d-109">Developer Exception Page</span></span>

<span data-ttu-id="0821d-110">*Geliştirici özel durum sayfası* , istek özel durumları hakkında ayrıntılı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0821d-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="0821d-111">ASP.NET Core şablonları aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0821d-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="0821d-112">Yukarıdaki vurgulanmış kod, uygulama [geliştirme ortamında](xref:fundamentals/environments)çalışırken geliştirici özel durum sayfasını sunar.</span><span class="sxs-lookup"><span data-stu-id="0821d-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0821d-113">Şablonlar, <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> izleyen ara yazılım içinde oluşturulan özel durumları yakalayabilmesi için, ara yazılım ardışık düzenine erken yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="0821d-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="0821d-114">Yukarıdaki kod, uygulama geliştirme ortamında çalıştırıldığında geliştirici özel durum sayfasını \* **yalnızca** _ öğesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="0821d-114">The preceding code enables the Developer Exception Page \* **only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="0821d-115">Ayrıntılı özel durum bilgileri, uygulama üretim ortamında çalıştırıldığında herkese açık bir şekilde gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="0821d-116">Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="0821d-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0821d-117">Geliştirici özel durum sayfası, özel durum ve istek hakkında şu bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="0821d-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="0821d-118">_ Yığın izleme</span><span class="sxs-lookup"><span data-stu-id="0821d-118">_ Stack trace</span></span>
* <span data-ttu-id="0821d-119">Varsa sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="0821d-119">Query string parameters if any</span></span>
* <span data-ttu-id="0821d-120">:::no-loc(Cookie):::varsa s</span><span class="sxs-lookup"><span data-stu-id="0821d-120">:::no-loc(Cookie):::s if any</span></span>
* <span data-ttu-id="0821d-121">Üst Bilgiler</span><span class="sxs-lookup"><span data-stu-id="0821d-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="0821d-122">Özel durum işleyici sayfası</span><span class="sxs-lookup"><span data-stu-id="0821d-122">Exception handler page</span></span>

<span data-ttu-id="0821d-123">[Üretim ortamı](xref:fundamentals/environments)için özel bir hata işleme sayfası yapılandırmak için çağrısı yapın <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="0821d-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="0821d-124">Bu özel durum işleme ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="0821d-124">This exception handling middleware:</span></span>

* <span data-ttu-id="0821d-125">Özel durumları yakalar ve günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0821d-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="0821d-126">Belirtilen yolu kullanarak isteği alternatif bir ardışık düzende yeniden yürütür.</span><span class="sxs-lookup"><span data-stu-id="0821d-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="0821d-127">Yanıt başlatılmışsa istek yeniden yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="0821d-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="0821d-128">Şablon tarafından oluşturulan kod, yolu kullanarak isteği yeniden yürütür `/Error` .</span><span class="sxs-lookup"><span data-stu-id="0821d-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="0821d-129">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> özel durum işleme ara yazılımını geliştirme olmayan ortamlara ekler:</span><span class="sxs-lookup"><span data-stu-id="0821d-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="0821d-130">:::no-loc(Razor):::Sayfalar uygulama şablonu, sayfalar klasöründe bir hata sayfası ( *. cshtml* ) ve <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> Sınıf ( `ErrorModel` ) sağlar *Pages* .</span><span class="sxs-lookup"><span data-stu-id="0821d-130">The :::no-loc(Razor)::: Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="0821d-131">MVC uygulaması için proje şablonu, `Error` giriş denetleyicisi için bir eylem yöntemi ve bir hata görünümü içerir.</span><span class="sxs-lookup"><span data-stu-id="0821d-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="0821d-132">Hata işleyicisi eylem yöntemini, gibi HTTP yöntemi öznitelikleriyle işaretlemeyin `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="0821d-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="0821d-133">Açık fiiller bazı isteklerin eylem yöntemine ulaşmasını önler.</span><span class="sxs-lookup"><span data-stu-id="0821d-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="0821d-134">Kimliği doğrulanmamış kullanıcılar hata görünümünü görüyse, metoda anonim erişime izin verin.</span><span class="sxs-lookup"><span data-stu-id="0821d-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="0821d-135">Özel duruma erişin</span><span class="sxs-lookup"><span data-stu-id="0821d-135">Access the exception</span></span>

<span data-ttu-id="0821d-136"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>Bir hata işleyicisindeki özel duruma ve özgün istek yoluna erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="0821d-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="0821d-137">Aşağıdaki kod, `ExceptionMessage` ASP.NET Core şablonları tarafından oluşturulan varsayılan *sayfalara/hata. cshtml. cs* öğesine ekler:</span><span class="sxs-lookup"><span data-stu-id="0821d-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="0821d-138">İstemcilere **not** hassas hata bilgileri sunma.</span><span class="sxs-lookup"><span data-stu-id="0821d-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="0821d-139">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="0821d-140">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)özel durumu sınamak için:</span><span class="sxs-lookup"><span data-stu-id="0821d-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="0821d-141">Ortamı üretime ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0821d-141">Set the environment to production.</span></span>
* <span data-ttu-id="0821d-142">Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<Startup>();` . *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="0821d-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs* .</span></span>
* <span data-ttu-id="0821d-143">Giriş sayfasında **özel durum tetikleme** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="0821d-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="0821d-144">Özel durum işleyici lambda</span><span class="sxs-lookup"><span data-stu-id="0821d-144">Exception handler lambda</span></span>

<span data-ttu-id="0821d-145">Özel bir özel [durum işleyici sayfasına](#exception-handler-page) bir alternatif, için bir lambda sağlamaktır <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="0821d-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="0821d-146">Lambda kullanılması, yanıtı döndürmeden önce hataya erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="0821d-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="0821d-147">Aşağıdaki kod, özel durum işleme için bir lambda kullanır:</span><span class="sxs-lookup"><span data-stu-id="0821d-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="0821d-148">Ya **not** da istemcilerinden önemli hata bilgileri sunma <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> .</span><span class="sxs-lookup"><span data-stu-id="0821d-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="0821d-149">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="0821d-150">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)lambda işlemesini test etmek için:</span><span class="sxs-lookup"><span data-stu-id="0821d-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="0821d-151">Ortamı üretime ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0821d-151">Set the environment to production.</span></span>
* <span data-ttu-id="0821d-152">Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<StartupLambda>();` . *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="0821d-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs* .</span></span>
* <span data-ttu-id="0821d-153">Giriş sayfasında **özel durum tetikleme** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="0821d-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="0821d-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0821d-154">UseStatusCodePages</span></span>

<span data-ttu-id="0821d-155">Varsayılan olarak, bir ASP.NET Core uygulama HTTP hata durum kodları için *404-bulunamadı* gibi bir durum kodu sayfası sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="0821d-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found* .</span></span> <span data-ttu-id="0821d-156">Uygulama, gövdesi olmayan bir HTTP 400-499 hata durumuyla karşılaştığında, durum kodunu ve boş bir yanıt gövdesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-156">When the app encounters an HTTP 400-499 error condition that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="0821d-157">Durum kodu sayfaları sağlamak için durum kodu sayfaları ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="0821d-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="0821d-158">Ortak hata durum kodları için varsayılan salt metin işleyicilerini etkinleştirmek üzere <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="0821d-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<!-- Review: 
When you comment out // UseExceptionHandler("/Error");`
you get a browser dependant error, not the codepage as I expected.
call /index/2 -> return StatusCode(500); -> you get the codepage 
-->

<span data-ttu-id="0821d-159">`UseStatusCodePages`İstek işleme ara yazılımı için çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="0821d-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="0821d-160">Örneğin, `UseStatusCodePages` statik dosya ara yazılımı ve uç nokta ara yazılımı aracılığıyla çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="0821d-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="0821d-161">`UseStatusCodePages`Kullanılmazsa, uç nokta olmadan BIR URL 'ye gitmek, uç noktanın bulunamadığını belirten tarayıcıya bağlı bir hata iletisi döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="0821d-162">Örneğin, sayfasına gitme `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="0821d-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="0821d-163">`UseStatusCodePages`Çağrıldığında tarayıcı şunu döndürür:</span><span class="sxs-lookup"><span data-stu-id="0821d-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="0821d-164">`UseStatusCodePages` Genellikle üretimde kullanılmaz çünkü kullanıcılara faydalı olmayan bir ileti döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="0821d-165">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için:</span><span class="sxs-lookup"><span data-stu-id="0821d-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="0821d-166">Ortamı üretime ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="0821d-166">Set the environment to production.</span></span>
* <span data-ttu-id="0821d-167">Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<StartupUseStatusCodePages>();` . *Program.cs*</span><span class="sxs-lookup"><span data-stu-id="0821d-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs* .</span></span>
* <span data-ttu-id="0821d-168">Giriş sayfasındaki giriş sayfasında bulunan bağlantıları seçin.</span><span class="sxs-lookup"><span data-stu-id="0821d-168">Select the links on the home page on the home page.</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="0821d-169">Biçim dizesiyle UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0821d-169">UseStatusCodePages with format string</span></span>

<span data-ttu-id="0821d-170">Yanıt içerik türünü ve metnini özelleştirmek için, öğesinin <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir içerik türü ve biçim dizesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0821d-170">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="0821d-171">Yukarıdaki kodda, `{0}` hata kodu için bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="0821d-171">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="0821d-172">`UseStatusCodePages` bir biçim dizesi, genellikle üretimde kullanılmıyor çünkü kullanıcılara faydalı olmayan bir ileti döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="0821d-172">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="0821d-173">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupFormat>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0821d-173">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs* .</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="0821d-174">Lambda ile UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0821d-174">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="0821d-175">Özel hata işleme ve yanıt yazma kodu belirtmek için, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir lambda ifadesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0821d-175">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="0821d-176">`UseStatusCodePages` Lambda ile, kullanıcılar için faydalı olmayan bir ileti döndürdüğünden, genellikle üretimde kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="0821d-176">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="0821d-177">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupStatusLambda>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0821d-177">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs* .</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="0821d-178">Usestatuscodepageswithyönlendirmeler</span><span class="sxs-lookup"><span data-stu-id="0821d-178">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="0821d-179"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0821d-179">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="0821d-180">İstemciye [302 tarafından bulunan](https://developer.mozilla.org/docs/Web/HTTP/Status/302) bir durum kodu gönderir.</span><span class="sxs-lookup"><span data-stu-id="0821d-180">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="0821d-181">İstemciyi, URL şablonunda belirtilen hata işleme uç noktasına yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="0821d-181">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="0821d-182">Hata işleme uç noktası genellikle hata bilgilerini görüntüler ve HTTP 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-182">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="0821d-183">URL şablonu, `{0}` Önceki kodda gösterildiği gibi durum kodu için bir yer tutucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-183">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="0821d-184">URL şablonu `~` (tilde) ile başlıyorsa, `~` uygulamanın tarafından değiştirilmiştir `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="0821d-184">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="0821d-185">Uygulamada bir uç nokta belirtirken, uç nokta için bir MVC görünümü veya :::no-loc(Razor)::: sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0821d-185">When specifying an endpoint in the app, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="0821d-186">Bir :::no-loc(Razor)::: Sayfalar örneği için bkz. [Sample App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)'Teki [Pages/mystatuscode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) .</span><span class="sxs-lookup"><span data-stu-id="0821d-186">For a :::no-loc(Razor)::: Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="0821d-187">Bu yöntem genellikle uygulama şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0821d-187">This method is commonly used when the app:</span></span>

* <span data-ttu-id="0821d-188">, Genellikle farklı bir uygulamanın hatayı işlediği durumlarda istemciyi farklı bir uç noktaya yönlendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-188">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="0821d-189">Web Apps için, istemcinin tarayıcı adres çubuğu yeniden yönlendirilen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="0821d-189">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="0821d-190">İlk yeniden yönlendirme yanıtıyla birlikte özgün durum kodunu korumamalıdır ve döndürmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-190">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="0821d-191">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupSCredirect>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0821d-191">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs* .</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="0821d-192">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="0821d-192">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="0821d-193"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0821d-193">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="0821d-194">İstemciye özgün durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-194">Returns the original status code to the client.</span></span>
* <span data-ttu-id="0821d-195">Alternatif bir yol kullanarak istek ardışık düzenini yeniden yürüterek yanıt gövdesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0821d-195">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="0821d-196">Uygulama içindeki bir uç nokta belirtilmişse, uç nokta için bir MVC görünümü veya :::no-loc(Razor)::: sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0821d-196">If an endpoint within the app is specified, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="0821d-197">`UseStatusCodePagesWithReExecute` `UseRouting` İsteğin durum sayfasına tekrar yönlendirilmemesi için önce bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="0821d-197">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="0821d-198">Bir :::no-loc(Razor)::: Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0821d-198">For a :::no-loc(Razor)::: Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="0821d-199">Bu yöntem genellikle uygulama şunları yaparken kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0821d-199">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="0821d-200">Farklı bir uç noktaya yönlendirmeye gerek kalmadan isteği işleyin.</span><span class="sxs-lookup"><span data-stu-id="0821d-200">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="0821d-201">Web Apps için, istemcinin tarayıcı adres çubuğu, ilk olarak istenen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="0821d-201">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="0821d-202">Yanıt ile orijinal durum kodunu koruyun ve geri döndürün.</span><span class="sxs-lookup"><span data-stu-id="0821d-202">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="0821d-203">URL ve sorgu dizesi şablonları, durum kodu için bir yer tutucu içerebilir `{0}` .</span><span class="sxs-lookup"><span data-stu-id="0821d-203">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="0821d-204">URL şablonu ile başlamalıdır `/` .</span><span class="sxs-lookup"><span data-stu-id="0821d-204">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a :::no-loc(Razor)::: Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="0821d-205">Hatayı işleyen uç nokta, aşağıdaki örnekte gösterildiği gibi, hatayı oluşturan özgün URL 'YI alabilir:</span><span class="sxs-lookup"><span data-stu-id="0821d-205">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="0821d-206">Bir :::no-loc(Razor)::: Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0821d-206">For a :::no-loc(Razor)::: Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="0821d-207">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupSCreX>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="0821d-207">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs* .</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="0821d-208">Durum kodu sayfalarını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="0821d-208">Disable status code pages</span></span>

<span data-ttu-id="0821d-209">MVC denetleyicisi veya eylem yöntemi için durum kodu sayfalarını devre dışı bırakmak için [[Skipstatuscodepages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="0821d-209">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="0821d-210">Bir :::no-loc(Razor)::: sayfa işleyici yöntemindeki veya BIR MVC denetleyicisindeki belirli istekler için durum kodu sayfalarını devre dışı bırakmak için şunu kullanın <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="0821d-210">To disable status code pages for specific requests in a :::no-loc(Razor)::: Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="0821d-211">Özel durum işleme kodu</span><span class="sxs-lookup"><span data-stu-id="0821d-211">Exception-handling code</span></span>

<span data-ttu-id="0821d-212">Özel durum işleme sayfalarındaki kod de özel durumlar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-212">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="0821d-213">Üretim hatası sayfaları tamamen test edilmelidir ve kendi özel durumlarını oluşturmaktan kaçınmak için ek bir işlem gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="0821d-213">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="0821d-214">Yanıt üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="0821d-214">Response headers</span></span>

<span data-ttu-id="0821d-215">Yanıt üstbilgileri gönderildikten sonra:</span><span class="sxs-lookup"><span data-stu-id="0821d-215">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="0821d-216">Uygulama, yanıtın durum kodunu değiştiremiyor.</span><span class="sxs-lookup"><span data-stu-id="0821d-216">The app can't change the response's status code.</span></span>
* <span data-ttu-id="0821d-217">Tüm özel durum sayfaları veya işleyicileri çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="0821d-217">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="0821d-218">Yanıt tamamlanmalıdır veya bağlantı iptal edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0821d-218">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="0821d-219">Sunucu özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="0821d-219">Server exception handling</span></span>

<span data-ttu-id="0821d-220">Bir uygulamadaki özel durum işleme mantığına ek olarak, [http sunucusu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-220">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="0821d-221">Yanıt üst bilgileri gönderilmeden sunucu bir özel durumu yakalarsa, sunucu yanıt `500 - Internal Server Error` gövdesi olmadan bir yanıt gönderir.</span><span class="sxs-lookup"><span data-stu-id="0821d-221">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="0821d-222">Yanıt üstbilgileri gönderildikten sonra sunucu bir özel durum yakalar, sunucu bağlantıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="0821d-222">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="0821d-223">Uygulama tarafından işlenmeyen istekler sunucu tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="0821d-223">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="0821d-224">Sunucu isteği gerçekleştirirken oluşan tüm özel durumlar sunucunun özel durum işleme tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="0821d-224">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="0821d-225">Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımı ve filtreler bu davranışı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="0821d-225">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="0821d-226">Başlatma özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="0821d-226">Startup exception handling</span></span>

<span data-ttu-id="0821d-227">Uygulama başlangıcında gerçekleşen özel durumları yalnızca barındırma katmanı işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-227">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="0821d-228">Konak, [Başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-228">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="0821d-229">Barındırma katmanı, yalnızca hatanın ana bilgisayar adresi/bağlantı noktası bağlamalarından sonra oluşması durumunda yakalanan başlatma hatası için bir hata sayfası gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-229">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="0821d-230">Bağlama başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="0821d-230">If binding fails:</span></span>

* <span data-ttu-id="0821d-231">Barındırma katmanı, kritik bir özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0821d-231">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="0821d-232">DotNet işlemi kilitleniyor.</span><span class="sxs-lookup"><span data-stu-id="0821d-232">The dotnet process crashes.</span></span>
* <span data-ttu-id="0821d-233">HTTP sunucusu [Kestrel](xref:fundamentals/servers/kestrel)olduğunda hata sayfası gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="0821d-233">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="0821d-234">[IIS](/iis) 'de (veya Azure App Service) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, işlem başlatılamazsa [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) tarafından *502,5 işlem hatası* döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0821d-234">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="0821d-235">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="0821d-235">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="0821d-236">Veritabanı hata sayfası</span><span class="sxs-lookup"><span data-stu-id="0821d-236">Database error page</span></span>

<span data-ttu-id="0821d-237">Veritabanı Geliştirici sayfası özel durum filtresi, `AddDatabaseDeveloperPageExceptionFilter` Entity Framework Core geçişleri kullanılarak çözümlenebilirler veritabanıyla ilgili özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="0821d-237">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="0821d-238">Bu özel durumlar oluştuğunda, sorunu çözmek için olası eylemlerin ayrıntıları ile bir HTML yanıtı üretilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-238">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="0821d-239">Bu sayfa yalnızca geliştirme ortamında etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="0821d-239">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="0821d-240">Aşağıdaki kod, :::no-loc(Razor)::: bireysel kullanıcı hesapları belirtildiğinde ASP.NET Core Pages şablonları tarafından oluşturulmuştur:</span><span class="sxs-lookup"><span data-stu-id="0821d-240">The following code was generated by the ASP.NET Core :::no-loc(Razor)::: Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="0821d-241">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="0821d-241">Exception filters</span></span>

<span data-ttu-id="0821d-242">MVC uygulamalarında özel durum filtreleri, genel olarak veya denetleyicide veya eylem temelinde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-242">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="0821d-243">:::no-loc(Razor):::Sayfalar uygulamalarda, genel olarak veya sayfa modeli başına yapılandırılabilirler.</span><span class="sxs-lookup"><span data-stu-id="0821d-243">In :::no-loc(Razor)::: Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="0821d-244">Bu filtreler, bir denetleyici eyleminin ya da başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumları işler.</span><span class="sxs-lookup"><span data-stu-id="0821d-244">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="0821d-245">Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="0821d-245">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="0821d-246">Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için faydalıdır, ancak yerleşik [özel durum işleme ara yazılımı](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)olarak esnek değildir `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="0821d-246">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="0821d-247">`UseExceptionHandler`HANGI MVC eyleminin seçilmesinden farklı olarak hata işleme yapmanız gerekmediği müddetçe kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="0821d-247">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="0821d-248">Model durumu hataları</span><span class="sxs-lookup"><span data-stu-id="0821d-248">Model state errors</span></span>

<span data-ttu-id="0821d-249">Model durumu hatalarını işleme hakkında daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) ve [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="0821d-249">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0821d-250">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0821d-250">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="0821d-251">[Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="0821d-251">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="0821d-252">Bu makalede ASP.NET Core Web Apps 'teki hataları işlemeye yönelik yaygın yaklaşımlar ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="0821d-252">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="0821d-253">Bkz <xref:web-api/handle-errors> . Web API 'leri için.</span><span class="sxs-lookup"><span data-stu-id="0821d-253">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="0821d-254">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="0821d-254">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="0821d-255">([Nasıl indirilir](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="0821d-255">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="0821d-256">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="0821d-256">Developer Exception Page</span></span>

<span data-ttu-id="0821d-257">*Geliştirici özel durum sayfası* , istek özel durumları hakkında ayrıntılı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="0821d-257">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="0821d-258">ASP.NET Core şablonları aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="0821d-258">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="0821d-259">Yukarıdaki kod, uygulama [geliştirme ortamında](xref:fundamentals/environments)çalışırken geliştirici özel durum sayfasını sunar.</span><span class="sxs-lookup"><span data-stu-id="0821d-259">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="0821d-260">Şablonlar, <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> izleyen ara yazılım içinde özel durumların yakalanabilmesi için herhangi bir ara yazılımın önüne yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="0821d-260">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="0821d-261">Yukarıdaki kod, geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken** sunar.</span><span class="sxs-lookup"><span data-stu-id="0821d-261">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment** .</span></span> <span data-ttu-id="0821d-262">Ayrıntılı özel durum bilgileri, uygulama üretimde çalıştırıldığında herkese açık bir şekilde gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-262">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="0821d-263">Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="0821d-263">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="0821d-264">Geliştirici özel durum sayfası, özel durum ve istek hakkında şu bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="0821d-264">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="0821d-265">Yığın izleme</span><span class="sxs-lookup"><span data-stu-id="0821d-265">Stack trace</span></span>
* <span data-ttu-id="0821d-266">Varsa sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="0821d-266">Query string parameters if any</span></span>
* <span data-ttu-id="0821d-267">:::no-loc(Cookie):::varsa s</span><span class="sxs-lookup"><span data-stu-id="0821d-267">:::no-loc(Cookie):::s if any</span></span>
* <span data-ttu-id="0821d-268">Üst Bilgiler</span><span class="sxs-lookup"><span data-stu-id="0821d-268">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="0821d-269">Özel durum işleyici sayfası</span><span class="sxs-lookup"><span data-stu-id="0821d-269">Exception handler page</span></span>

<span data-ttu-id="0821d-270">Üretim ortamı için özel bir hata işleme sayfası yapılandırmak için özel durum Işleme ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="0821d-270">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="0821d-271">Ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="0821d-271">The middleware:</span></span>

* <span data-ttu-id="0821d-272">Özel durumları yakalar ve günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0821d-272">Catches and logs exceptions.</span></span>
* <span data-ttu-id="0821d-273">İsteği, belirtilen sayfa veya denetleyici için alternatif bir ardışık düzende yeniden yürütür.</span><span class="sxs-lookup"><span data-stu-id="0821d-273">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="0821d-274">Yanıt başlatılmışsa istek yeniden yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="0821d-274">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="0821d-275">Oluşturulan şablon kodu, isteği yeniden yürütür `/Error` .</span><span class="sxs-lookup"><span data-stu-id="0821d-275">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="0821d-276">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> özel durum Işleme ara yazılımını geliştirme olmayan ortamlara ekler:</span><span class="sxs-lookup"><span data-stu-id="0821d-276">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="0821d-277">:::no-loc(Razor):::Sayfalar uygulama şablonu, sayfalar klasöründe bir hata sayfası ( *. cshtml* ) ve <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> Sınıf ( `ErrorModel` ) sağlar *Pages* .</span><span class="sxs-lookup"><span data-stu-id="0821d-277">The :::no-loc(Razor)::: Pages app template provides an Error page ( *.cshtml* ) and <xref:Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::Pages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="0821d-278">MVC uygulaması için proje şablonu, ana denetleyicide bir hata eylemi yöntemi ve bir hata görünümü içerir.</span><span class="sxs-lookup"><span data-stu-id="0821d-278">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="0821d-279">Hata işleyicisi eylem yöntemini, gibi HTTP yöntemi öznitelikleriyle işaretlemeyin `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="0821d-279">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="0821d-280">Açık fiiller bazı isteklerin yönteme ulaşmasını önler.</span><span class="sxs-lookup"><span data-stu-id="0821d-280">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="0821d-281">Kimliği doğrulanmamış kullanıcılar hata görünümünü görüyse, metoda anonim erişime izin verin.</span><span class="sxs-lookup"><span data-stu-id="0821d-281">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="0821d-282">Özel duruma erişin</span><span class="sxs-lookup"><span data-stu-id="0821d-282">Access the exception</span></span>

<span data-ttu-id="0821d-283"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>Hata işleyicisi denetleyicisi veya sayfasındaki özel duruma ve özgün istek yoluna erişmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="0821d-283">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="0821d-284">İstemcilere **not** hassas hata bilgileri sunma.</span><span class="sxs-lookup"><span data-stu-id="0821d-284">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="0821d-285">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-285">Serving errors is a security risk.</span></span>

<span data-ttu-id="0821d-286">Önceki özel durum işleme sayfasını tetiklemek için ortamı üretimler olarak ayarlayın ve bir özel durum zorlayın.</span><span class="sxs-lookup"><span data-stu-id="0821d-286">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="0821d-287">Özel durum işleyici lambda</span><span class="sxs-lookup"><span data-stu-id="0821d-287">Exception handler lambda</span></span>

<span data-ttu-id="0821d-288">Özel bir özel [durum işleyici sayfasına](#exception-handler-page) bir alternatif, için bir lambda sağlamaktır <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="0821d-288">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="0821d-289">Lambda kullanılması, yanıtı döndürmeden önce hataya erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="0821d-289">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="0821d-290">Özel durum işleme için lambda kullanmanın bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="0821d-290">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="0821d-291">Yukarıdaki kodda, `await context.Response.WriteAsync(new string(' ', 512));` Internet Explorer tarayıcısı BIR IE hata iletisi yerine hata iletisini görüntüleyecek şekilde eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="0821d-291">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="0821d-292">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16144)bakın.</span><span class="sxs-lookup"><span data-stu-id="0821d-292">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="0821d-293">Ya **not** da istemcilerinden önemli hata bilgileri sunma <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> .</span><span class="sxs-lookup"><span data-stu-id="0821d-293">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="0821d-294">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="0821d-295">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)lambda işlemenin özel durum işleme sonucunu görmek için, `ProdEnvironment` ve ön `ErrorHandlerLambda` işlemci yönergelerini kullanın ve giriş sayfasında **özel durum Tetikle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="0821d-295">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="0821d-296">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0821d-296">UseStatusCodePages</span></span>

<span data-ttu-id="0821d-297">Varsayılan olarak, bir ASP.NET Core uygulama HTTP durum kodları için *404-bulunamadı* gibi bir durum kodu sayfası sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="0821d-297">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found* .</span></span> <span data-ttu-id="0821d-298">Uygulama bir durum kodu ve boş bir yanıt gövdesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-298">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="0821d-299">Durum kodu sayfaları sağlamak için durum kodu sayfaları ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="0821d-299">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="0821d-300">Ara yazılım [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi tarafından kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-300">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="0821d-301">Ortak hata durum kodları için varsayılan salt metin işleyicilerini etkinleştirmek üzere <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="0821d-301">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="0821d-302">`UseStatusCodePages`İstek işleme ara yazılımı (örneğin, statik dosya ara yazılımı ve MVC ara yazılımı) önce çağırın.</span><span class="sxs-lookup"><span data-stu-id="0821d-302">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="0821d-303">`UseStatusCodePages`Kullanılmazsa, uç nokta olmadan BIR URL 'ye gitmek, uç noktanın bulunamadığını belirten tarayıcıya bağlı bir hata iletisi döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-303">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="0821d-304">Örneğin, sayfasına gitme `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="0821d-304">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="0821d-305">`UseStatusCodePages`Çağrıldığında tarayıcı şunu döndürür:</span><span class="sxs-lookup"><span data-stu-id="0821d-305">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="0821d-306">Biçim dizesiyle UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0821d-306">UseStatusCodePages with format string</span></span>

<span data-ttu-id="0821d-307">Yanıt içerik türünü ve metnini özelleştirmek için, öğesinin <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir içerik türü ve biçim dizesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0821d-307">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="0821d-308">Lambda ile UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="0821d-308">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="0821d-309">Özel hata işleme ve yanıt yazma kodu belirtmek için, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir lambda ifadesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="0821d-309">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="0821d-310">Usestatuscodepageswithyönlendirmeler</span><span class="sxs-lookup"><span data-stu-id="0821d-310">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="0821d-311"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0821d-311">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="0821d-312">İstemciye *302 tarafından bulunan* bir durum kodu gönderir.</span><span class="sxs-lookup"><span data-stu-id="0821d-312">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="0821d-313">İstemciyi, URL şablonunda belirtilen konuma yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="0821d-313">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="0821d-314">URL şablonu, `{0}` örnekte gösterildiği gibi durum kodu için bir yer tutucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-314">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="0821d-315">URL şablonu `~` (tilde) ile başlıyorsa, `~` uygulamanın tarafından değiştirilmiştir `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="0821d-315">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="0821d-316">Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya :::no-loc(Razor)::: sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0821d-316">If you point to an endpoint within the app, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="0821d-317">Bir :::no-loc(Razor)::: Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0821d-317">For a :::no-loc(Razor)::: Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="0821d-318">Bu yöntem genellikle uygulama şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0821d-318">This method is commonly used when the app:</span></span>

* <span data-ttu-id="0821d-319">, Genellikle farklı bir uygulamanın hatayı işlediği durumlarda istemciyi farklı bir uç noktaya yönlendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-319">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="0821d-320">Web Apps için, istemcinin tarayıcı adres çubuğu yeniden yönlendirilen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="0821d-320">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="0821d-321">İlk yeniden yönlendirme yanıtıyla birlikte özgün durum kodunu korumamalıdır ve döndürmemelidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-321">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="0821d-322">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="0821d-322">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="0821d-323"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="0821d-323">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="0821d-324">İstemciye özgün durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="0821d-324">Returns the original status code to the client.</span></span>
* <span data-ttu-id="0821d-325">Alternatif bir yol kullanarak istek ardışık düzenini yeniden yürüterek yanıt gövdesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="0821d-325">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="0821d-326">Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya :::no-loc(Razor)::: sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="0821d-326">If you point to an endpoint within the app, create an MVC view or :::no-loc(Razor)::: page for the endpoint.</span></span> <span data-ttu-id="0821d-327">`UseStatusCodePagesWithReExecute` `UseRouting` İsteğin durum sayfasına tekrar yönlendirilmemesi için önce bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="0821d-327">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="0821d-328">Bir :::no-loc(Razor)::: Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="0821d-328">For a :::no-loc(Razor)::: Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="0821d-329">Bu yöntem genellikle uygulama şunları yaparken kullanılır:</span><span class="sxs-lookup"><span data-stu-id="0821d-329">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="0821d-330">Farklı bir uç noktaya yönlendirmeye gerek kalmadan isteği işleyin.</span><span class="sxs-lookup"><span data-stu-id="0821d-330">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="0821d-331">Web Apps için, istemcinin tarayıcı adres çubuğu, ilk olarak istenen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="0821d-331">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="0821d-332">Yanıt ile orijinal durum kodunu koruyun ve geri döndürün.</span><span class="sxs-lookup"><span data-stu-id="0821d-332">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="0821d-333">URL ve sorgu dizesi şablonları durum kodu için bir yer tutucu ( `{0}` ) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-333">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="0821d-334">URL şablonu eğik çizgiyle ( `/` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="0821d-334">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="0821d-335">Yolda bir yer tutucu kullanırken, uç noktanın (sayfa veya denetleyicinin) yol kesimini işleyediğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="0821d-335">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="0821d-336">Örneğin, bir :::no-loc(Razor)::: sayfa, yönergeyle isteğe bağlı yol segmenti değerini kabul etmelidir `@page` :</span><span class="sxs-lookup"><span data-stu-id="0821d-336">For example, a :::no-loc(Razor)::: Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="0821d-337">Hatayı işleyen uç nokta, aşağıdaki örnekte gösterildiği gibi, hatayı oluşturan özgün URL 'YI alabilir:</span><span class="sxs-lookup"><span data-stu-id="0821d-337">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="0821d-338">Durum kodu sayfalarını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="0821d-338">Disable status code pages</span></span>

<span data-ttu-id="0821d-339">MVC denetleyicisi veya eylem yöntemi için durum kodu sayfalarını devre dışı bırakmak için özniteliğini kullanın [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="0821d-339">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="0821d-340">Bir :::no-loc(Razor)::: sayfa işleyici yöntemindeki veya BIR MVC denetleyicisindeki belirli istekler için durum kodu sayfalarını devre dışı bırakmak için şunu kullanın <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="0821d-340">To disable status code pages for specific requests in a :::no-loc(Razor)::: Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="0821d-341">Özel durum işleme kodu</span><span class="sxs-lookup"><span data-stu-id="0821d-341">Exception-handling code</span></span>

<span data-ttu-id="0821d-342">Özel durum işleme sayfalarındaki kod özel durumlar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-342">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="0821d-343">Üretim hatası sayfalarının tamamen statik içerikten oluşması genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="0821d-343">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="0821d-344">Yanıt üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="0821d-344">Response headers</span></span>

<span data-ttu-id="0821d-345">Yanıt üstbilgileri gönderildikten sonra:</span><span class="sxs-lookup"><span data-stu-id="0821d-345">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="0821d-346">Uygulama, yanıtın durum kodunu değiştiremiyor.</span><span class="sxs-lookup"><span data-stu-id="0821d-346">The app can't change the response's status code.</span></span>
* <span data-ttu-id="0821d-347">Tüm özel durum sayfaları veya işleyicileri çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="0821d-347">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="0821d-348">Yanıt tamamlanmalıdır veya bağlantı iptal edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="0821d-348">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="0821d-349">Sunucu özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="0821d-349">Server exception handling</span></span>

<span data-ttu-id="0821d-350">Uygulamanızın özel durum işleme mantığına ek olarak, [http sunucusu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-350">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="0821d-351">Sunucu yanıt üstbilgileri gönderilmeden önce bir özel durum yakalarsa, sunucu yanıt gövdesi olmadan *500-Iç sunucu hatası* yanıtı gönderir.</span><span class="sxs-lookup"><span data-stu-id="0821d-351">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="0821d-352">Yanıt üstbilgileri gönderildikten sonra sunucu bir özel durum yakalar, sunucu bağlantıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="0821d-352">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="0821d-353">Uygulamanız tarafından işlenmeyen istekler sunucu tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="0821d-353">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="0821d-354">Sunucu isteği gerçekleştirirken oluşan tüm özel durumlar sunucunun özel durum işleme tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="0821d-354">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="0821d-355">Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımı ve filtreler bu davranışı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="0821d-355">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="0821d-356">Başlatma özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="0821d-356">Startup exception handling</span></span>

<span data-ttu-id="0821d-357">Uygulama başlangıcında gerçekleşen özel durumları yalnızca barındırma katmanı işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-357">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="0821d-358">Konak, [Başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-358">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="0821d-359">Barındırma katmanı, yalnızca hatanın ana bilgisayar adresi/bağlantı noktası bağlamalarından sonra oluşması durumunda yakalanan başlatma hatası için bir hata sayfası gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-359">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="0821d-360">Bağlama başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="0821d-360">If binding fails:</span></span>

* <span data-ttu-id="0821d-361">Barındırma katmanı, kritik bir özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="0821d-361">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="0821d-362">DotNet işlemi kilitleniyor.</span><span class="sxs-lookup"><span data-stu-id="0821d-362">The dotnet process crashes.</span></span>
* <span data-ttu-id="0821d-363">HTTP sunucusu [Kestrel](xref:fundamentals/servers/kestrel)olduğunda hata sayfası gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="0821d-363">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="0821d-364">[IIS](/iis) 'de (veya Azure App Service) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, işlem başlatılamazsa [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) tarafından *502,5 işlem hatası* döndürülür.</span><span class="sxs-lookup"><span data-stu-id="0821d-364">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="0821d-365">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="0821d-365">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="0821d-366">Veritabanı hata sayfası</span><span class="sxs-lookup"><span data-stu-id="0821d-366">Database error page</span></span>

<span data-ttu-id="0821d-367">Veritabanı hata sayfası ara yazılımı, Entity Framework geçişleri kullanılarak çözümleneyolabilecek veritabanıyla ilgili özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="0821d-367">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="0821d-368">Bu özel durumlar gerçekleştiğinde, sorunu çözmeye yönelik olası eylemlerin ayrıntılarını içeren bir HTML yanıtı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="0821d-368">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="0821d-369">Bu sayfa yalnızca geliştirme ortamında etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="0821d-369">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="0821d-370">Aşağıdakileri kod ekleyerek sayfayı etkinleştirin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="0821d-370">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="0821d-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>[Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="0821d-371"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="0821d-372">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="0821d-372">Exception filters</span></span>

<span data-ttu-id="0821d-373">MVC uygulamalarında özel durum filtreleri, genel olarak veya denetleyicide veya eylem temelinde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="0821d-373">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="0821d-374">:::no-loc(Razor):::Sayfalar uygulamalarda, genel olarak veya sayfa modeli başına yapılandırılabilirler.</span><span class="sxs-lookup"><span data-stu-id="0821d-374">In :::no-loc(Razor)::: Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="0821d-375">Bu filtreler, bir denetleyici eyleminin veya başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumları işler.</span><span class="sxs-lookup"><span data-stu-id="0821d-375">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="0821d-376">Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="0821d-376">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="0821d-377">Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için yararlıdır, ancak özel durum Işleme ara yazılımı kadar esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="0821d-377">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="0821d-378">Ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="0821d-378">We recommend using the middleware.</span></span> <span data-ttu-id="0821d-379">Yalnızca hangi MVC eyleminin seçilmesinden ayrı olarak hata işleme yapmanız gereken durumlarda filtreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="0821d-379">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="0821d-380">Model durumu hataları</span><span class="sxs-lookup"><span data-stu-id="0821d-380">Model state errors</span></span>

<span data-ttu-id="0821d-381">Model durumu hatalarını işleme hakkında daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) ve [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="0821d-381">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0821d-382">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="0821d-382">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
