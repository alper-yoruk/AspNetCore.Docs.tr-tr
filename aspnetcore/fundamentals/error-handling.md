---
title: ASP.NET Core hataları işleme
author: rick-anderson
description: ASP.NET Core uygulamalarında hataların nasıl işleneceğini öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- appsettings.json
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
uid: fundamentals/error-handling
ms.openlocfilehash: e65983fb1a440057283111ea5a79a79b765607b7
ms.sourcegitcommit: 610936e4d3507f7f3d467ed7859ab9354ec158ba
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/25/2021
ms.locfileid: "98751684"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="b1b83-103">ASP.NET Core hataları işleme</span><span class="sxs-lookup"><span data-stu-id="b1b83-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="b1b83-104">[Kirk Larkabağı](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="b1b83-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b1b83-105">Bu makalede ASP.NET Core Web Apps 'teki hataları işlemeye yönelik yaygın yaklaşımlar ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="b1b83-106">Bkz <xref:web-api/handle-errors> . Web API 'leri için.</span><span class="sxs-lookup"><span data-stu-id="b1b83-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="b1b83-107">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b1b83-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="b1b83-108">([Nasıl indirilir](xref:index#how-to-download-a-sample).) F12 tarayıcı geliştirici araçlarındaki ağ sekmesi, örnek uygulamayı test ederken faydalıdır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="b1b83-109">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="b1b83-109">Developer Exception Page</span></span>

<span data-ttu-id="b1b83-110">*Geliştirici özel durum sayfası* , istek özel durumları hakkında ayrıntılı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b1b83-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="b1b83-111">ASP.NET Core şablonları aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b1b83-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="b1b83-112">Yukarıdaki vurgulanmış kod, uygulama [geliştirme ortamında](xref:fundamentals/environments)çalışırken geliştirici özel durum sayfasını sunar.</span><span class="sxs-lookup"><span data-stu-id="b1b83-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b1b83-113">Şablonlar, <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> izleyen ara yazılım içinde oluşturulan özel durumları yakalayabilmesi için, ara yazılım ardışık düzenine erken yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="b1b83-114">Yukarıdaki kod, uygulama geliştirme ortamında çalıştırıldığında geliştirici özel durum sayfasını \***yalnızca** _ öğesine izin vermez.</span><span class="sxs-lookup"><span data-stu-id="b1b83-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="b1b83-115">Ayrıntılı özel durum bilgileri, uygulama üretim ortamında çalıştırıldığında herkese açık bir şekilde gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="b1b83-116">Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="b1b83-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b1b83-117">Geliştirici özel durum sayfası, özel durum ve istek hakkında şu bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="b1b83-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="b1b83-118">_ Yığın izleme</span><span class="sxs-lookup"><span data-stu-id="b1b83-118">_ Stack trace</span></span>
* <span data-ttu-id="b1b83-119">Varsa sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="b1b83-119">Query string parameters if any</span></span>
* <span data-ttu-id="b1b83-120">Cookievarsa s</span><span class="sxs-lookup"><span data-stu-id="b1b83-120">Cookies if any</span></span>
* <span data-ttu-id="b1b83-121">Üst Bilgiler</span><span class="sxs-lookup"><span data-stu-id="b1b83-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="b1b83-122">Özel durum işleyici sayfası</span><span class="sxs-lookup"><span data-stu-id="b1b83-122">Exception handler page</span></span>

<span data-ttu-id="b1b83-123">[Üretim ortamı](xref:fundamentals/environments)için özel bir hata işleme sayfası yapılandırmak için çağrısı yapın <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="b1b83-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b1b83-124">Bu özel durum işleme ara yazılımı:</span><span class="sxs-lookup"><span data-stu-id="b1b83-124">This exception handling middleware:</span></span>

* <span data-ttu-id="b1b83-125">Özel durumları yakalar ve günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b1b83-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="b1b83-126">Belirtilen yolu kullanarak isteği alternatif bir ardışık düzende yeniden yürütür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="b1b83-127">Yanıt başlatılmışsa istek yeniden yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="b1b83-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="b1b83-128">Şablon tarafından oluşturulan kod, yolu kullanarak isteği yeniden yürütür `/Error` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="b1b83-129">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> özel durum işleme ara yazılımını geliştirme olmayan ortamlara ekler:</span><span class="sxs-lookup"><span data-stu-id="b1b83-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="b1b83-130">RazorSayfalar uygulama şablonu, sayfalar klasöründe bir hata sayfası (*. cshtml*) ve <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Sınıf ( `ErrorModel` ) sağlar  .</span><span class="sxs-lookup"><span data-stu-id="b1b83-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="b1b83-131">MVC uygulaması için proje şablonu, `Error` giriş denetleyicisi için bir eylem yöntemi ve bir hata görünümü içerir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="b1b83-132">Özel durum işleme ara yazılımı, *özgün* http yöntemini kullanarak isteği yeniden yürütür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-132">The exception handling middleware re-executes the request using the *original* HTTP method.</span></span> <span data-ttu-id="b1b83-133">Bir hata işleyicisi uç noktası belirli bir HTTP yöntemleri kümesiyle sınırlı ise, yalnızca bu HTTP yöntemleri için çalışır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-133">If an error handler endpoint is restricted to a specific set of HTTP methods, it runs only for those HTTP methods.</span></span> <span data-ttu-id="b1b83-134">Örneğin, özniteliğini kullanan bir MVC denetleyici eylemi `[HttpGet]` yalnızca get istekleri için çalışır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-134">For example, an MVC controller action that uses the `[HttpGet]` attribute runs only for GET requests.</span></span> <span data-ttu-id="b1b83-135">*Tüm* isteklerin özel hata işleme sayfasına ulaşmasını sağlamak için bunları belırlı bir http yöntemleri kümesiyle kısıtlamayın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-135">To ensure that *all* requests reach the custom error handling page, don't restrict them to a specific set of HTTP methods.</span></span>

<span data-ttu-id="b1b83-136">Özgün HTTP yöntemine göre özel durumları farklı şekilde işlemek için:</span><span class="sxs-lookup"><span data-stu-id="b1b83-136">To handle exceptions differently based on the original HTTP method:</span></span>

* <span data-ttu-id="b1b83-137">RazorSayfalar için birden çok işleyici yöntemi oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-137">For Razor Pages, create multiple handler methods.</span></span> <span data-ttu-id="b1b83-138">Örneğin, özel durum `OnGet` Al işlemini idare etmek ve `OnPost` özel durumları işlemek için kullanmak üzere kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-138">For example, use `OnGet` to handle GET exceptions and use `OnPost` to handle POST exceptions.</span></span>
* <span data-ttu-id="b1b83-139">MVC için, HTTP fiili özniteliklerini birden çok eyleme uygulayın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-139">For MVC, apply HTTP verb attributes to multiple actions.</span></span> <span data-ttu-id="b1b83-140">Örneğin, özel durum `[HttpGet]` Al işlemini idare etmek ve `[HttpPost]` özel durumları işlemek için kullanmak üzere kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-140">For example, use `[HttpGet]` to handle GET exceptions and use `[HttpPost]` to handle POST exceptions.</span></span>

<span data-ttu-id="b1b83-141">Kimliği doğrulanmamış kullanıcıların özel hata işleme sayfasını görüntülemesine izin vermek için, anonim erişimi desteklediğinden emin olun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-141">To allow unauthenticated users to view the custom error handling page, ensure that it supports anonymous access.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="b1b83-142">Özel duruma erişin</span><span class="sxs-lookup"><span data-stu-id="b1b83-142">Access the exception</span></span>

<span data-ttu-id="b1b83-143"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>Bir hata işleyicisindeki özel duruma ve özgün istek yoluna erişmek için kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-143">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="b1b83-144">Aşağıdaki kod, `ExceptionMessage` ASP.NET Core şablonları tarafından oluşturulan varsayılan *sayfalara/hata. cshtml. cs* öğesine ekler:</span><span class="sxs-lookup"><span data-stu-id="b1b83-144">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="b1b83-145">İstemcilere  hassas hata bilgileri sunma.</span><span class="sxs-lookup"><span data-stu-id="b1b83-145">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="b1b83-146">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-146">Serving errors is a security risk.</span></span>

<span data-ttu-id="b1b83-147">[Örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)özel durumu sınamak için:</span><span class="sxs-lookup"><span data-stu-id="b1b83-147">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b1b83-148">Ortamı üretime ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-148">Set the environment to production.</span></span>
* <span data-ttu-id="b1b83-149">Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<Startup>();` . </span><span class="sxs-lookup"><span data-stu-id="b1b83-149">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b1b83-150">Giriş sayfasında **özel durum tetikleme** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="b1b83-150">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="b1b83-151">Özel durum işleyici lambda</span><span class="sxs-lookup"><span data-stu-id="b1b83-151">Exception handler lambda</span></span>

<span data-ttu-id="b1b83-152">Özel bir özel [durum işleyici sayfasına](#exception-handler-page) bir alternatif, için bir lambda sağlamaktır <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="b1b83-152">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b1b83-153">Lambda kullanılması, yanıtı döndürmeden önce hataya erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="b1b83-153">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="b1b83-154">Aşağıdaki kod, özel durum işleme için bir lambda kullanır:</span><span class="sxs-lookup"><span data-stu-id="b1b83-154">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="b1b83-155">Ya  da istemcilerinden önemli hata bilgileri sunma <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> .</span><span class="sxs-lookup"><span data-stu-id="b1b83-155">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="b1b83-156">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-156">Serving errors is a security risk.</span></span>

<span data-ttu-id="b1b83-157">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)lambda işlemesini test etmek için:</span><span class="sxs-lookup"><span data-stu-id="b1b83-157">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b1b83-158">Ortamı üretime ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-158">Set the environment to production.</span></span>
* <span data-ttu-id="b1b83-159">Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<StartupLambda>();` . </span><span class="sxs-lookup"><span data-stu-id="b1b83-159">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b1b83-160">Giriş sayfasında **özel durum tetikleme** ' yı seçin.</span><span class="sxs-lookup"><span data-stu-id="b1b83-160">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="b1b83-161">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b1b83-161">UseStatusCodePages</span></span>

<span data-ttu-id="b1b83-162">Varsayılan olarak, bir ASP.NET Core uygulama HTTP hata durum kodları için *404-bulunamadı* gibi bir durum kodu sayfası sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="b1b83-162">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="b1b83-163">Uygulama, gövdesi olmayan bir HTTP 400-599 hata durum kodu ile karşılaştığında, durum kodunu ve boş bir yanıt gövdesini döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-163">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="b1b83-164">Durum kodu sayfaları sağlamak için durum kodu sayfaları ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-164">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="b1b83-165">Ortak hata durum kodları için varsayılan salt metin işleyicilerini etkinleştirmek üzere <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="b1b83-165">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="b1b83-166">`UseStatusCodePages`İstek işleme ara yazılımı için çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-166">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="b1b83-167">Örneğin, `UseStatusCodePages` statik dosya ara yazılımı ve uç nokta ara yazılımı aracılığıyla çağrı yapın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-167">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="b1b83-168">`UseStatusCodePages`Kullanılmazsa, uç nokta olmadan BIR URL 'ye gitmek, uç noktanın bulunamadığını belirten tarayıcıya bağlı bir hata iletisi döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-168">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="b1b83-169">Örneğin, sayfasına gitme `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-169">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="b1b83-170">`UseStatusCodePages`Çağrıldığında tarayıcı şunu döndürür:</span><span class="sxs-lookup"><span data-stu-id="b1b83-170">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="b1b83-171">`UseStatusCodePages` Genellikle üretimde kullanılmaz çünkü kullanıcılara faydalı olmayan bir ileti döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-171">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b1b83-172">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için:</span><span class="sxs-lookup"><span data-stu-id="b1b83-172">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="b1b83-173">Ortamı üretime ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-173">Set the environment to production.</span></span>
* <span data-ttu-id="b1b83-174">Program.cs içindeki açıklamaları kaldırın `webBuilder.UseStartup<StartupUseStatusCodePages>();` . </span><span class="sxs-lookup"><span data-stu-id="b1b83-174">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="b1b83-175">Giriş sayfasındaki giriş sayfasında bulunan bağlantıları seçin.</span><span class="sxs-lookup"><span data-stu-id="b1b83-175">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="b1b83-176">Durum kodu sayfaları ara yazılımı özel durumları **yakalamaz** .</span><span class="sxs-lookup"><span data-stu-id="b1b83-176">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="b1b83-177">Özel bir hata işleme sayfası sağlamak için [özel durum işleyicisi sayfasını](#exception-handler-page)kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-177">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="b1b83-178">Biçim dizesiyle UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b1b83-178">UseStatusCodePages with format string</span></span>

<span data-ttu-id="b1b83-179">Yanıt içerik türünü ve metnini özelleştirmek için, öğesinin <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir içerik türü ve biçim dizesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b1b83-179">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="b1b83-180">Yukarıdaki kodda, `{0}` hata kodu için bir yer tutucudur.</span><span class="sxs-lookup"><span data-stu-id="b1b83-180">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="b1b83-181">`UseStatusCodePages` bir biçim dizesi, genellikle üretimde kullanılmıyor çünkü kullanıcılara faydalı olmayan bir ileti döndürüyor.</span><span class="sxs-lookup"><span data-stu-id="b1b83-181">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b1b83-182">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupFormat>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-182">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="b1b83-183">Lambda ile UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b1b83-183">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="b1b83-184">Özel hata işleme ve yanıt yazma kodu belirtmek için, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir lambda ifadesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b1b83-184">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="b1b83-185">`UseStatusCodePages` Lambda ile, kullanıcılar için faydalı olmayan bir ileti döndürdüğünden, genellikle üretimde kullanılmaz.</span><span class="sxs-lookup"><span data-stu-id="b1b83-185">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="b1b83-186">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupStatusLambda>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-186">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="b1b83-187">Usestatuscodepageswithyönlendirmeler</span><span class="sxs-lookup"><span data-stu-id="b1b83-187">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="b1b83-188"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b1b83-188">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="b1b83-189">İstemciye [302 tarafından bulunan](https://developer.mozilla.org/docs/Web/HTTP/Status/302) bir durum kodu gönderir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-189">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="b1b83-190">İstemciyi, URL şablonunda belirtilen hata işleme uç noktasına yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-190">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="b1b83-191">Hata işleme uç noktası genellikle hata bilgilerini görüntüler ve HTTP 200 döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-191">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="b1b83-192">URL şablonu, `{0}` Önceki kodda gösterildiği gibi durum kodu için bir yer tutucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-192">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="b1b83-193">URL şablonu `~` (tilde) ile başlıyorsa, `~` uygulamanın tarafından değiştirilmiştir `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-193">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="b1b83-194">Uygulamada bir uç nokta belirtirken, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-194">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b1b83-195">Bir Razor Sayfalar örneği için bkz. [Sample App](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)'Teki [Pages/mystatuscode. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) .</span><span class="sxs-lookup"><span data-stu-id="b1b83-195">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b1b83-196">Bu yöntem genellikle uygulama şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b1b83-196">This method is commonly used when the app:</span></span>

* <span data-ttu-id="b1b83-197">, Genellikle farklı bir uygulamanın hatayı işlediği durumlarda istemciyi farklı bir uç noktaya yönlendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-197">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="b1b83-198">Web Apps için, istemcinin tarayıcı adres çubuğu yeniden yönlendirilen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-198">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="b1b83-199">İlk yeniden yönlendirme yanıtıyla birlikte özgün durum kodunu korumamalıdır ve döndürmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-199">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="b1b83-200">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupSCredirect>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-200">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="b1b83-201">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="b1b83-201">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="b1b83-202"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b1b83-202">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="b1b83-203">İstemciye özgün durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-203">Returns the original status code to the client.</span></span>
* <span data-ttu-id="b1b83-204">Alternatif bir yol kullanarak istek ardışık düzenini yeniden yürüterek yanıt gövdesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b1b83-204">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="b1b83-205">Uygulama içindeki bir uç nokta belirtilmişse, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-205">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b1b83-206">`UseStatusCodePagesWithReExecute` `UseRouting` İsteğin durum sayfasına tekrar yönlendirilmemesi için önce bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-206">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="b1b83-207">Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-207">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b1b83-208">Bu yöntem genellikle uygulama şunları yaparken kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b1b83-208">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="b1b83-209">Farklı bir uç noktaya yönlendirmeye gerek kalmadan isteği işleyin.</span><span class="sxs-lookup"><span data-stu-id="b1b83-209">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="b1b83-210">Web Apps için, istemcinin tarayıcı adres çubuğu, ilk olarak istenen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-210">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="b1b83-211">Yanıt ile orijinal durum kodunu koruyun ve geri döndürün.</span><span class="sxs-lookup"><span data-stu-id="b1b83-211">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="b1b83-212">URL ve sorgu dizesi şablonları, durum kodu için bir yer tutucu içerebilir `{0}` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-212">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="b1b83-213">URL şablonu ile başlamalıdır `/` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-213">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="b1b83-214">Hatayı işleyen uç nokta, aşağıdaki örnekte gösterildiği gibi, hatayı oluşturan özgün URL 'YI alabilir:</span><span class="sxs-lookup"><span data-stu-id="b1b83-214">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="b1b83-215">Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x) [Pages/MyStatusCode2. cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-215">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="b1b83-216">`UseStatusCodePages` [Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)test etmek için, `webBuilder.UseStartup<StartupSCreX>();` *program.cs* içindeki açıklamaları kaldırın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-216">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="b1b83-217">Durum kodu sayfalarını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b1b83-217">Disable status code pages</span></span>

<span data-ttu-id="b1b83-218">MVC denetleyicisi veya eylem yöntemi için durum kodu sayfalarını devre dışı bırakmak için [[Skipstatuscodepages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) özniteliğini kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-218">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="b1b83-219">Bir Razor sayfa işleyici yöntemindeki veya BIR MVC denetleyicisindeki belirli istekler için durum kodu sayfalarını devre dışı bırakmak için şunu kullanın <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="b1b83-219">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="b1b83-220">Özel durum işleme kodu</span><span class="sxs-lookup"><span data-stu-id="b1b83-220">Exception-handling code</span></span>

<span data-ttu-id="b1b83-221">Özel durum işleme sayfalarındaki kod de özel durumlar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-221">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="b1b83-222">Üretim hatası sayfaları tamamen test edilmelidir ve kendi özel durumlarını oluşturmaktan kaçınmak için ek bir işlem gerçekleştirir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-222">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="b1b83-223">Yanıt üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="b1b83-223">Response headers</span></span>

<span data-ttu-id="b1b83-224">Yanıt üstbilgileri gönderildikten sonra:</span><span class="sxs-lookup"><span data-stu-id="b1b83-224">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="b1b83-225">Uygulama, yanıtın durum kodunu değiştiremiyor.</span><span class="sxs-lookup"><span data-stu-id="b1b83-225">The app can't change the response's status code.</span></span>
* <span data-ttu-id="b1b83-226">Tüm özel durum sayfaları veya işleyicileri çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="b1b83-226">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="b1b83-227">Yanıt tamamlanmalıdır veya bağlantı iptal edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-227">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="b1b83-228">Sunucu özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="b1b83-228">Server exception handling</span></span>

<span data-ttu-id="b1b83-229">Bir uygulamadaki özel durum işleme mantığına ek olarak, [http sunucusu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-229">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="b1b83-230">Yanıt üst bilgileri gönderilmeden sunucu bir özel durumu yakalarsa, sunucu yanıt `500 - Internal Server Error` gövdesi olmadan bir yanıt gönderir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-230">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="b1b83-231">Yanıt üstbilgileri gönderildikten sonra sunucu bir özel durum yakalar, sunucu bağlantıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-231">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="b1b83-232">Uygulama tarafından işlenmeyen istekler sunucu tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-232">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="b1b83-233">Sunucu isteği gerçekleştirirken oluşan tüm özel durumlar sunucunun özel durum işleme tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-233">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="b1b83-234">Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımı ve filtreler bu davranışı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="b1b83-234">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="b1b83-235">Başlatma özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="b1b83-235">Startup exception handling</span></span>

<span data-ttu-id="b1b83-236">Uygulama başlangıcında gerçekleşen özel durumları yalnızca barındırma katmanı işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-236">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="b1b83-237">Konak, [Başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-237">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="b1b83-238">Barındırma katmanı, yalnızca hatanın ana bilgisayar adresi/bağlantı noktası bağlamalarından sonra oluşması durumunda yakalanan başlatma hatası için bir hata sayfası gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-238">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="b1b83-239">Bağlama başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="b1b83-239">If binding fails:</span></span>

* <span data-ttu-id="b1b83-240">Barındırma katmanı, kritik bir özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b1b83-240">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="b1b83-241">DotNet işlemi kilitleniyor.</span><span class="sxs-lookup"><span data-stu-id="b1b83-241">The dotnet process crashes.</span></span>
* <span data-ttu-id="b1b83-242">HTTP sunucusu [Kestrel](xref:fundamentals/servers/kestrel)olduğunda hata sayfası gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="b1b83-242">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="b1b83-243">[IIS](/iis) 'de (veya Azure App Service) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, işlem başlatılamazsa [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) tarafından *502,5 işlem hatası* döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-243">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="b1b83-244">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b1b83-244">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="b1b83-245">Veritabanı hata sayfası</span><span class="sxs-lookup"><span data-stu-id="b1b83-245">Database error page</span></span>

<span data-ttu-id="b1b83-246">Veritabanı Geliştirici sayfası özel durum filtresi, `AddDatabaseDeveloperPageExceptionFilter` Entity Framework Core geçişleri kullanılarak çözümlenebilirler veritabanıyla ilgili özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="b1b83-246">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="b1b83-247">Bu özel durumlar oluştuğunda, sorunu çözmek için olası eylemlerin ayrıntıları ile bir HTML yanıtı üretilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-247">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="b1b83-248">Bu sayfa yalnızca geliştirme ortamında etkinleştirilmiştir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-248">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="b1b83-249">Aşağıdaki kod, Razor bireysel kullanıcı hesapları belirtildiğinde ASP.NET Core Pages şablonları tarafından oluşturulmuştur:</span><span class="sxs-lookup"><span data-stu-id="b1b83-249">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="b1b83-250">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="b1b83-250">Exception filters</span></span>

<span data-ttu-id="b1b83-251">MVC uygulamalarında özel durum filtreleri, genel olarak veya denetleyicide veya eylem temelinde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-251">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="b1b83-252">RazorSayfalar uygulamalarda, genel olarak veya sayfa modeli başına yapılandırılabilirler.</span><span class="sxs-lookup"><span data-stu-id="b1b83-252">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="b1b83-253">Bu filtreler, bir denetleyici eyleminin ya da başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumları işler.</span><span class="sxs-lookup"><span data-stu-id="b1b83-253">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="b1b83-254">Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="b1b83-254">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="b1b83-255">Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için faydalıdır, ancak yerleşik [özel durum işleme ara yazılımı](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs)olarak esnek değildir `UseExceptionHandler` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-255">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="b1b83-256">`UseExceptionHandler`HANGI MVC eyleminin seçilmesinden farklı olarak hata işleme yapmanız gerekmediği müddetçe kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b1b83-256">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="b1b83-257">Model durumu hataları</span><span class="sxs-lookup"><span data-stu-id="b1b83-257">Model state errors</span></span>

<span data-ttu-id="b1b83-258">Model durumu hatalarını işleme hakkında daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) ve [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b1b83-258">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1b83-259">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b1b83-259">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="b1b83-260">[Tom Dykstra](https://github.com/tdykstra/)ve [Steve Smith](https://ardalis.com/) tarafından</span><span class="sxs-lookup"><span data-stu-id="b1b83-260">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b1b83-261">Bu makalede ASP.NET Core Web Apps 'teki hataları işlemeye yönelik yaygın yaklaşımlar ele alınmaktadır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-261">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="b1b83-262">Bkz <xref:web-api/handle-errors> . Web API 'leri için.</span><span class="sxs-lookup"><span data-stu-id="b1b83-262">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="b1b83-263">[Örnek kodu görüntüleyin veya indirin](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span><span class="sxs-lookup"><span data-stu-id="b1b83-263">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="b1b83-264">([Nasıl indirilir](xref:index#how-to-download-a-sample).)</span><span class="sxs-lookup"><span data-stu-id="b1b83-264">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="b1b83-265">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="b1b83-265">Developer Exception Page</span></span>

<span data-ttu-id="b1b83-266">*Geliştirici özel durum sayfası* , istek özel durumları hakkında ayrıntılı bilgileri görüntüler.</span><span class="sxs-lookup"><span data-stu-id="b1b83-266">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="b1b83-267">ASP.NET Core şablonları aşağıdaki kodu oluşturur:</span><span class="sxs-lookup"><span data-stu-id="b1b83-267">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="b1b83-268">Yukarıdaki kod, uygulama [geliştirme ortamında](xref:fundamentals/environments)çalışırken geliştirici özel durum sayfasını sunar.</span><span class="sxs-lookup"><span data-stu-id="b1b83-268">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="b1b83-269">Şablonlar, <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> izleyen ara yazılım içinde özel durumların yakalanabilmesi için herhangi bir ara yazılımın önüne yerleştirir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-269">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="b1b83-270">Yukarıdaki kod, geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken** sunar.</span><span class="sxs-lookup"><span data-stu-id="b1b83-270">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="b1b83-271">Ayrıntılı özel durum bilgileri, uygulama üretimde çalıştırıldığında herkese açık bir şekilde gösterilmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-271">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="b1b83-272">Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="b1b83-272">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="b1b83-273">Geliştirici özel durum sayfası, özel durum ve istek hakkında şu bilgileri içerir:</span><span class="sxs-lookup"><span data-stu-id="b1b83-273">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="b1b83-274">Yığın izleme</span><span class="sxs-lookup"><span data-stu-id="b1b83-274">Stack trace</span></span>
* <span data-ttu-id="b1b83-275">Varsa sorgu dizesi parametreleri</span><span class="sxs-lookup"><span data-stu-id="b1b83-275">Query string parameters if any</span></span>
* <span data-ttu-id="b1b83-276">Cookievarsa s</span><span class="sxs-lookup"><span data-stu-id="b1b83-276">Cookies if any</span></span>
* <span data-ttu-id="b1b83-277">Üst Bilgiler</span><span class="sxs-lookup"><span data-stu-id="b1b83-277">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="b1b83-278">Özel durum işleyici sayfası</span><span class="sxs-lookup"><span data-stu-id="b1b83-278">Exception handler page</span></span>

<span data-ttu-id="b1b83-279">Üretim ortamı için özel bir hata işleme sayfası yapılandırmak için özel durum Işleme ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-279">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="b1b83-280">Ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="b1b83-280">The middleware:</span></span>

* <span data-ttu-id="b1b83-281">Özel durumları yakalar ve günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b1b83-281">Catches and logs exceptions.</span></span>
* <span data-ttu-id="b1b83-282">İsteği, belirtilen sayfa veya denetleyici için alternatif bir ardışık düzende yeniden yürütür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-282">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="b1b83-283">Yanıt başlatılmışsa istek yeniden yürütülmez.</span><span class="sxs-lookup"><span data-stu-id="b1b83-283">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="b1b83-284">Oluşturulan şablon kodu, isteği yeniden yürütür `/Error` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-284">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="b1b83-285">Aşağıdaki örnekte, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> özel durum Işleme ara yazılımını geliştirme olmayan ortamlara ekler:</span><span class="sxs-lookup"><span data-stu-id="b1b83-285">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="b1b83-286">RazorSayfalar uygulama şablonu, sayfalar klasöründe bir hata sayfası (*. cshtml*) ve <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Sınıf ( `ErrorModel` ) sağlar  .</span><span class="sxs-lookup"><span data-stu-id="b1b83-286">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="b1b83-287">MVC uygulaması için proje şablonu, ana denetleyicide bir hata eylemi yöntemi ve bir hata görünümü içerir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-287">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="b1b83-288">Hata işleyicisi eylem yöntemini, gibi HTTP yöntemi öznitelikleriyle işaretlemeyin `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-288">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="b1b83-289">Açık fiiller bazı isteklerin yönteme ulaşmasını önler.</span><span class="sxs-lookup"><span data-stu-id="b1b83-289">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="b1b83-290">Kimliği doğrulanmamış kullanıcılar hata görünümünü görüyse, metoda anonim erişime izin verin.</span><span class="sxs-lookup"><span data-stu-id="b1b83-290">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="b1b83-291">Özel duruma erişin</span><span class="sxs-lookup"><span data-stu-id="b1b83-291">Access the exception</span></span>

<span data-ttu-id="b1b83-292"><xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature>Hata işleyicisi denetleyicisi veya sayfasındaki özel duruma ve özgün istek yoluna erişmek için kullanın:</span><span class="sxs-lookup"><span data-stu-id="b1b83-292">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="b1b83-293">İstemcilere  hassas hata bilgileri sunma.</span><span class="sxs-lookup"><span data-stu-id="b1b83-293">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="b1b83-294">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-294">Serving errors is a security risk.</span></span>

<span data-ttu-id="b1b83-295">Önceki özel durum işleme sayfasını tetiklemek için ortamı üretimler olarak ayarlayın ve bir özel durum zorlayın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-295">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="b1b83-296">Özel durum işleyici lambda</span><span class="sxs-lookup"><span data-stu-id="b1b83-296">Exception handler lambda</span></span>

<span data-ttu-id="b1b83-297">Özel bir özel [durum işleyici sayfasına](#exception-handler-page) bir alternatif, için bir lambda sağlamaktır <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> .</span><span class="sxs-lookup"><span data-stu-id="b1b83-297">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="b1b83-298">Lambda kullanılması, yanıtı döndürmeden önce hataya erişim sağlar.</span><span class="sxs-lookup"><span data-stu-id="b1b83-298">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="b1b83-299">Özel durum işleme için lambda kullanmanın bir örneği aşağıda verilmiştir:</span><span class="sxs-lookup"><span data-stu-id="b1b83-299">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="b1b83-300">Yukarıdaki kodda, `await context.Response.WriteAsync(new string(' ', 512));` Internet Explorer tarayıcısı BIR IE hata iletisi yerine hata iletisini görüntüleyecek şekilde eklenmiştir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-300">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="b1b83-301">Daha fazla bilgi için [Bu GitHub sorununa](https://github.com/dotnet/AspNetCore.Docs/issues/16144)bakın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-301">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="b1b83-302">Ya  da istemcilerinden önemli hata bilgileri sunma <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> .</span><span class="sxs-lookup"><span data-stu-id="b1b83-302">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="b1b83-303">Hatalara hizmet vermek bir güvenlik riskidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-303">Serving errors is a security risk.</span></span>

<span data-ttu-id="b1b83-304">[Örnek uygulamada](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)lambda işlemenin özel durum işleme sonucunu görmek için, `ProdEnvironment` ve ön `ErrorHandlerLambda` işlemci yönergelerini kullanın ve giriş sayfasında **özel durum Tetikle** ' yi seçin.</span><span class="sxs-lookup"><span data-stu-id="b1b83-304">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="b1b83-305">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b1b83-305">UseStatusCodePages</span></span>

<span data-ttu-id="b1b83-306">Varsayılan olarak, bir ASP.NET Core uygulama HTTP durum kodları için *404-bulunamadı* gibi bir durum kodu sayfası sağlamaz.</span><span class="sxs-lookup"><span data-stu-id="b1b83-306">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="b1b83-307">Uygulama bir durum kodu ve boş bir yanıt gövdesi döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-307">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="b1b83-308">Durum kodu sayfaları sağlamak için durum kodu sayfaları ara yazılımını kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-308">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="b1b83-309">Ara yazılım [Microsoft. AspNetCore. Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) paketi tarafından kullanılabilir hale getirilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-309">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="b1b83-310">Ortak hata durum kodları için varsayılan salt metin işleyicilerini etkinleştirmek üzere <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> `Startup.Configure` yöntemi çağırın:</span><span class="sxs-lookup"><span data-stu-id="b1b83-310">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="b1b83-311">`UseStatusCodePages`İstek işleme ara yazılımı (örneğin, statik dosya ara yazılımı ve MVC ara yazılımı) önce çağırın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-311">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="b1b83-312">`UseStatusCodePages`Kullanılmazsa, uç nokta olmadan BIR URL 'ye gitmek, uç noktanın bulunamadığını belirten tarayıcıya bağlı bir hata iletisi döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-312">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="b1b83-313">Örneğin, sayfasına gitme `Home/Privacy2` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-313">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="b1b83-314">`UseStatusCodePages`Çağrıldığında tarayıcı şunu döndürür:</span><span class="sxs-lookup"><span data-stu-id="b1b83-314">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="b1b83-315">Biçim dizesiyle UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b1b83-315">UseStatusCodePages with format string</span></span>

<span data-ttu-id="b1b83-316">Yanıt içerik türünü ve metnini özelleştirmek için, öğesinin <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir içerik türü ve biçim dizesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b1b83-316">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="b1b83-317">Lambda ile UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="b1b83-317">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="b1b83-318">Özel hata işleme ve yanıt yazma kodu belirtmek için, <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> bir lambda ifadesi alan aşırı yüklemesini kullanın:</span><span class="sxs-lookup"><span data-stu-id="b1b83-318">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="b1b83-319">Usestatuscodepageswithyönlendirmeler</span><span class="sxs-lookup"><span data-stu-id="b1b83-319">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="b1b83-320"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b1b83-320">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="b1b83-321">İstemciye *302 tarafından bulunan* bir durum kodu gönderir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-321">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="b1b83-322">İstemciyi, URL şablonunda belirtilen konuma yönlendirir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-322">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="b1b83-323">URL şablonu, `{0}` örnekte gösterildiği gibi durum kodu için bir yer tutucu içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-323">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="b1b83-324">URL şablonu `~` (tilde) ile başlıyorsa, `~` uygulamanın tarafından değiştirilmiştir `PathBase` .</span><span class="sxs-lookup"><span data-stu-id="b1b83-324">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="b1b83-325">Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-325">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b1b83-326">Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-326">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="b1b83-327">Bu yöntem genellikle uygulama şu şekilde kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b1b83-327">This method is commonly used when the app:</span></span>

* <span data-ttu-id="b1b83-328">, Genellikle farklı bir uygulamanın hatayı işlediği durumlarda istemciyi farklı bir uç noktaya yönlendirmelidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-328">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="b1b83-329">Web Apps için, istemcinin tarayıcı adres çubuğu yeniden yönlendirilen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-329">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="b1b83-330">İlk yeniden yönlendirme yanıtıyla birlikte özgün durum kodunu korumamalıdır ve döndürmemelidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-330">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="b1b83-331">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="b1b83-331">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="b1b83-332"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A>Uzantı yöntemi:</span><span class="sxs-lookup"><span data-stu-id="b1b83-332">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="b1b83-333">İstemciye özgün durum kodunu döndürür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-333">Returns the original status code to the client.</span></span>
* <span data-ttu-id="b1b83-334">Alternatif bir yol kullanarak istek ardışık düzenini yeniden yürüterek yanıt gövdesini oluşturur.</span><span class="sxs-lookup"><span data-stu-id="b1b83-334">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="b1b83-335">Uygulamanın içindeki bir uç noktayı işaret ederseniz, uç nokta için bir MVC görünümü veya Razor sayfası oluşturun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-335">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="b1b83-336">`UseStatusCodePagesWithReExecute` `UseRouting` İsteğin durum sayfasına tekrar yönlendirilmemesi için önce bulunduğundan emin olun.</span><span class="sxs-lookup"><span data-stu-id="b1b83-336">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="b1b83-337">Bir Razor Sayfalar örneği için [örnek uygulamadaki](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples) *Pages/StatusCode. cshtml* bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-337">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="b1b83-338">Bu yöntem genellikle uygulama şunları yaparken kullanılır:</span><span class="sxs-lookup"><span data-stu-id="b1b83-338">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="b1b83-339">Farklı bir uç noktaya yönlendirmeye gerek kalmadan isteği işleyin.</span><span class="sxs-lookup"><span data-stu-id="b1b83-339">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="b1b83-340">Web Apps için, istemcinin tarayıcı adres çubuğu, ilk olarak istenen uç noktayı yansıtır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-340">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="b1b83-341">Yanıt ile orijinal durum kodunu koruyun ve geri döndürün.</span><span class="sxs-lookup"><span data-stu-id="b1b83-341">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="b1b83-342">URL ve sorgu dizesi şablonları durum kodu için bir yer tutucu ( `{0}` ) içerebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-342">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="b1b83-343">URL şablonu eğik çizgiyle ( `/` ) başlamalıdır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-343">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="b1b83-344">Yolda bir yer tutucu kullanırken, uç noktanın (sayfa veya denetleyicinin) yol kesimini işleyediğini doğrulayın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-344">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="b1b83-345">Örneğin, bir Razor sayfa, yönergeyle isteğe bağlı yol segmenti değerini kabul etmelidir `@page` :</span><span class="sxs-lookup"><span data-stu-id="b1b83-345">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="b1b83-346">Hatayı işleyen uç nokta, aşağıdaki örnekte gösterildiği gibi, hatayı oluşturan özgün URL 'YI alabilir:</span><span class="sxs-lookup"><span data-stu-id="b1b83-346">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="b1b83-347">Durum kodu sayfalarını devre dışı bırak</span><span class="sxs-lookup"><span data-stu-id="b1b83-347">Disable status code pages</span></span>

<span data-ttu-id="b1b83-348">MVC denetleyicisi veya eylem yöntemi için durum kodu sayfalarını devre dışı bırakmak için özniteliğini kullanın [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) .</span><span class="sxs-lookup"><span data-stu-id="b1b83-348">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="b1b83-349">Bir Razor sayfa işleyici yöntemindeki veya BIR MVC denetleyicisindeki belirli istekler için durum kodu sayfalarını devre dışı bırakmak için şunu kullanın <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> :</span><span class="sxs-lookup"><span data-stu-id="b1b83-349">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="b1b83-350">Özel durum işleme kodu</span><span class="sxs-lookup"><span data-stu-id="b1b83-350">Exception-handling code</span></span>

<span data-ttu-id="b1b83-351">Özel durum işleme sayfalarındaki kod özel durumlar oluşturabilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-351">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="b1b83-352">Üretim hatası sayfalarının tamamen statik içerikten oluşması genellikle iyi bir fikirdir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-352">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="b1b83-353">Yanıt üst bilgileri</span><span class="sxs-lookup"><span data-stu-id="b1b83-353">Response headers</span></span>

<span data-ttu-id="b1b83-354">Yanıt üstbilgileri gönderildikten sonra:</span><span class="sxs-lookup"><span data-stu-id="b1b83-354">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="b1b83-355">Uygulama, yanıtın durum kodunu değiştiremiyor.</span><span class="sxs-lookup"><span data-stu-id="b1b83-355">The app can't change the response's status code.</span></span>
* <span data-ttu-id="b1b83-356">Tüm özel durum sayfaları veya işleyicileri çalıştırılamaz.</span><span class="sxs-lookup"><span data-stu-id="b1b83-356">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="b1b83-357">Yanıt tamamlanmalıdır veya bağlantı iptal edilmiş olmalıdır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-357">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="b1b83-358">Sunucu özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="b1b83-358">Server exception handling</span></span>

<span data-ttu-id="b1b83-359">Uygulamanızın özel durum işleme mantığına ek olarak, [http sunucusu uygulaması](xref:fundamentals/servers/index) bazı özel durumları işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-359">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="b1b83-360">Sunucu yanıt üstbilgileri gönderilmeden önce bir özel durum yakalarsa, sunucu yanıt gövdesi olmadan *500-Iç sunucu hatası* yanıtı gönderir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-360">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="b1b83-361">Yanıt üstbilgileri gönderildikten sonra sunucu bir özel durum yakalar, sunucu bağlantıyı kapatır.</span><span class="sxs-lookup"><span data-stu-id="b1b83-361">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="b1b83-362">Uygulamanız tarafından işlenmeyen istekler sunucu tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-362">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="b1b83-363">Sunucu isteği gerçekleştirirken oluşan tüm özel durumlar sunucunun özel durum işleme tarafından işlenir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-363">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="b1b83-364">Uygulamanın özel hata sayfaları, özel durum işleme ara yazılımı ve filtreler bu davranışı etkilemez.</span><span class="sxs-lookup"><span data-stu-id="b1b83-364">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="b1b83-365">Başlatma özel durum işleme</span><span class="sxs-lookup"><span data-stu-id="b1b83-365">Startup exception handling</span></span>

<span data-ttu-id="b1b83-366">Uygulama başlangıcında gerçekleşen özel durumları yalnızca barındırma katmanı işleyebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-366">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="b1b83-367">Konak, [Başlangıç hatalarını yakalamak](xref:fundamentals/host/web-host#capture-startup-errors) ve [ayrıntılı hataları yakalamak](xref:fundamentals/host/web-host#detailed-errors)için yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-367">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="b1b83-368">Barındırma katmanı, yalnızca hatanın ana bilgisayar adresi/bağlantı noktası bağlamalarından sonra oluşması durumunda yakalanan başlatma hatası için bir hata sayfası gösterebilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-368">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="b1b83-369">Bağlama başarısız olursa:</span><span class="sxs-lookup"><span data-stu-id="b1b83-369">If binding fails:</span></span>

* <span data-ttu-id="b1b83-370">Barındırma katmanı, kritik bir özel durumu günlüğe kaydeder.</span><span class="sxs-lookup"><span data-stu-id="b1b83-370">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="b1b83-371">DotNet işlemi kilitleniyor.</span><span class="sxs-lookup"><span data-stu-id="b1b83-371">The dotnet process crashes.</span></span>
* <span data-ttu-id="b1b83-372">HTTP sunucusu [Kestrel](xref:fundamentals/servers/kestrel)olduğunda hata sayfası gösterilmez.</span><span class="sxs-lookup"><span data-stu-id="b1b83-372">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="b1b83-373">[IIS](/iis) 'de (veya Azure App Service) veya [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview)çalışırken, işlem başlatılamazsa [ASP.NET Core modülü](xref:host-and-deploy/aspnet-core-module) tarafından *502,5 işlem hatası* döndürülür.</span><span class="sxs-lookup"><span data-stu-id="b1b83-373">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="b1b83-374">Daha fazla bilgi için bkz. <xref:test/troubleshoot-azure-iis>.</span><span class="sxs-lookup"><span data-stu-id="b1b83-374">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="b1b83-375">Veritabanı hata sayfası</span><span class="sxs-lookup"><span data-stu-id="b1b83-375">Database error page</span></span>

<span data-ttu-id="b1b83-376">Veritabanı hata sayfası ara yazılımı, Entity Framework geçişleri kullanılarak çözümleneyolabilecek veritabanıyla ilgili özel durumları yakalar.</span><span class="sxs-lookup"><span data-stu-id="b1b83-376">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="b1b83-377">Bu özel durumlar gerçekleştiğinde, sorunu çözmeye yönelik olası eylemlerin ayrıntılarını içeren bir HTML yanıtı oluşturulur.</span><span class="sxs-lookup"><span data-stu-id="b1b83-377">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="b1b83-378">Bu sayfa yalnızca geliştirme ortamında etkinleştirilmelidir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-378">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="b1b83-379">Aşağıdakileri kod ekleyerek sayfayı etkinleştirin `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="b1b83-379">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="b1b83-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A>[Microsoft. AspNetCore. Diagnostics. EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet paketini gerektirir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-380"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="b1b83-381">Özel durum filtreleri</span><span class="sxs-lookup"><span data-stu-id="b1b83-381">Exception filters</span></span>

<span data-ttu-id="b1b83-382">MVC uygulamalarında özel durum filtreleri, genel olarak veya denetleyicide veya eylem temelinde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-382">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="b1b83-383">RazorSayfalar uygulamalarda, genel olarak veya sayfa modeli başına yapılandırılabilirler.</span><span class="sxs-lookup"><span data-stu-id="b1b83-383">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="b1b83-384">Bu filtreler, bir denetleyici eyleminin veya başka bir filtrenin yürütülmesi sırasında oluşan işlenmemiş özel durumları işler.</span><span class="sxs-lookup"><span data-stu-id="b1b83-384">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="b1b83-385">Daha fazla bilgi için bkz. <xref:mvc/controllers/filters#exception-filters>.</span><span class="sxs-lookup"><span data-stu-id="b1b83-385">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="b1b83-386">Özel durum filtreleri, MVC eylemleri içinde oluşan özel durumları yakalamak için yararlıdır, ancak özel durum Işleme ara yazılımı kadar esnek değildir.</span><span class="sxs-lookup"><span data-stu-id="b1b83-386">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="b1b83-387">Ara yazılımı kullanmanızı öneririz.</span><span class="sxs-lookup"><span data-stu-id="b1b83-387">We recommend using the middleware.</span></span> <span data-ttu-id="b1b83-388">Yalnızca hangi MVC eyleminin seçilmesinden ayrı olarak hata işleme yapmanız gereken durumlarda filtreleri kullanın.</span><span class="sxs-lookup"><span data-stu-id="b1b83-388">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="b1b83-389">Model durumu hataları</span><span class="sxs-lookup"><span data-stu-id="b1b83-389">Model state errors</span></span>

<span data-ttu-id="b1b83-390">Model durumu hatalarını işleme hakkında daha fazla bilgi için bkz. [model bağlama](xref:mvc/models/model-binding) ve [model doğrulaması](xref:mvc/models/validation).</span><span class="sxs-lookup"><span data-stu-id="b1b83-390">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b1b83-391">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="b1b83-391">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
