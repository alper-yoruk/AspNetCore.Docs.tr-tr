---
title: ASP.NET Core Web API 'Lerinde hataları işleme
author: pranavkm
description: ASP.NET Core Web API 'Leri ile hata işleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 1/11/2021
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
uid: web-api/handle-errors
ms.openlocfilehash: 92e9350a7892f8f38f64d4ebd68d54a97ec7e994
ms.sourcegitcommit: 97243663fd46c721660e77ef652fe2190a461f81
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 01/09/2021
ms.locfileid: "98058382"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="44089-103">ASP.NET Core Web API 'Lerinde hataları işleme</span><span class="sxs-lookup"><span data-stu-id="44089-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="44089-104">Bu makalede, ASP.NET Core Web API 'Leriyle hata işlemenin nasıl işleneceği ve özelleştirileceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="44089-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="44089-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="44089-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="44089-106">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="44089-106">Developer Exception Page</span></span>

<span data-ttu-id="44089-107">[Geliştirici özel durum sayfası](xref:fundamentals/error-handling) , sunucu hataları için ayrıntılı yığın izlemeleri almak için kullanışlı bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="44089-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="44089-108">Bu <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> , http ardışık düzeninde zaman uyumlu ve zaman uyumsuz özel durumları yakalamak ve hata yanıtları oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="44089-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="44089-109">Göstermek için aşağıdaki denetleyici eylemini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="44089-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="44089-110">`curl`Önceki eylemi test etmek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="44089-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44089-111">ASP.NET Core 3,0 ve üzeri sürümlerde geliştirici özel durum sayfasında, istemci HTML biçimli çıkış isteğinde yoksa bir düz metin yanıtı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="44089-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="44089-112">Aşağıdaki çıkış görüntülenir:</span><span class="sxs-lookup"><span data-stu-id="44089-112">The following output appears:</span></span>

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/plain
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:13:16 GMT

System.ArgumentException: We don't offer a weather forecast for chicago. (Parameter 'city')
   at WebApiSample.Controllers.WeatherForecastController.Get(String city) in C:\working_folder\aspnet\AspNetCore.Docs\aspnetcore\web-api\handle-errors\samples\3.x\Controllers\WeatherForecastController.cs:line 34
   at lambda_method(Closure , Object , Object[] )
   at Microsoft.Extensions.Internal.ObjectMethodExecutor.Execute(Object target, Object[] parameters)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ActionMethodExecutor.SyncObjectResultExecutor.Execute(IActionResultTypeMapper mapper, ObjectMethodExecutor executor, Object controller, Object[] arguments)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeActionMethodAsync>g__Logged|12_1(ControllerActionInvoker invoker)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.<InvokeNextActionFilterAsync>g__Awaited|10_0(ControllerActionInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Rethrow(ActionExecutedContextSealed context)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.Next(State& next, Scope& scope, Object& state, Boolean& isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker.InvokeInnerFilterAsync()
--- End of stack trace from previous location where exception was thrown ---
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeFilterPipelineAsync>g__Awaited|19_0(ResourceInvoker invoker, Task lastTask, State next, Scope scope, Object state, Boolean isCompleted)
   at Microsoft.AspNetCore.Mvc.Infrastructure.ResourceInvoker.<InvokeAsync>g__Logged|17_1(ResourceInvoker invoker)
   at Microsoft.AspNetCore.Routing.EndpointMiddleware.<Invoke>g__AwaitRequestTask|6_0(Endpoint endpoint, Task requestTask, ILogger logger)
   at Microsoft.AspNetCore.Authorization.AuthorizationMiddleware.Invoke(HttpContext context)
   at Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware.Invoke(HttpContext context)

HEADERS
=======
Accept: */*
Host: localhost:44312
User-Agent: curl/7.55.1
```

<span data-ttu-id="44089-113">Bunun yerine HTML biçimli bir yanıt göstermek için, `Accept` http istek üst bilgisini `text/html` medya türüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="44089-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="44089-114">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="44089-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="44089-115">HTTP yanıtından aşağıdaki alıntıyı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="44089-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="44089-116">ASP.NET Core 2,2 ve önceki sürümlerde, geliştirici özel durum sayfasında HTML biçimli bir yanıt görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="44089-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="44089-117">Örneğin, HTTP yanıtından aşağıdaki alıntıyı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="44089-117">For example, consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

```console
HTTP/1.1 500 Internal Server Error
Transfer-Encoding: chunked
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
X-Powered-By: ASP.NET
Date: Fri, 27 Sep 2019 16:55:37 GMT

<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml">
    <head>
        <meta charset="utf-8" />
        <title>Internal Server Error</title>
        <style>
            body {
    font-family: 'Segoe UI', Tahoma, Arial, Helvetica, sans-serif;
    font-size: .813em;
    color: #222;
    background-color: #fff;
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44089-118">HTML biçimli yanıt Postman gibi araçlar aracılığıyla test edilirken yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="44089-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="44089-119">Aşağıdaki ekran yakalama, Postman 'daki düz metin ve HTML biçimli yanıtları gösterir:</span><span class="sxs-lookup"><span data-stu-id="44089-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Geliştirici özel durum sayfası Postman 'da test ediliyor](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="44089-121">Geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken** etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="44089-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="44089-122">Uygulama üretimde çalıştırıldığında ayrıntılı özel durum bilgilerini herkese açık bir şekilde paylaşmayın.</span><span class="sxs-lookup"><span data-stu-id="44089-122">Don't share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="44089-123">Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="44089-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>
>
> <span data-ttu-id="44089-124">Hata işleyicisi eylem yöntemini, gibi HTTP yöntemi öznitelikleriyle işaretlemeyin `HttpGet` .</span><span class="sxs-lookup"><span data-stu-id="44089-124">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="44089-125">Açık fiiller bazı isteklerin eylem yöntemine ulaşmasını önler.</span><span class="sxs-lookup"><span data-stu-id="44089-125">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="44089-126">Kimliği doğrulanmamış kullanıcılar hatayı görebilmelidir yönteme anonim erişime izin verin.</span><span class="sxs-lookup"><span data-stu-id="44089-126">Allow anonymous access to the method if unauthenticated users should see the error.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="44089-127">Özel durum işleyicisi</span><span class="sxs-lookup"><span data-stu-id="44089-127">Exception handler</span></span>

<span data-ttu-id="44089-128">Geliştirme dışı ortamlarda, [özel durum Işleme ara yazılımı](xref:fundamentals/error-handling) bir hata yükü oluşturmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="44089-128">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="44089-129">' De `Startup.Configure` , <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ara yazılımı kullanmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="44089-129">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="44089-130">Yola yanıt vermek için bir denetleyici eylemi yapılandırın `/error` :</span><span class="sxs-lookup"><span data-stu-id="44089-130">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="44089-131">Yukarıdaki `Error` eylem Istemciye [RFC 7807](https://tools.ietf.org/html/rfc7807)ile uyumlu bir yük gönderir.</span><span class="sxs-lookup"><span data-stu-id="44089-131">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="44089-132">Özel durum Işleme ara yazılımı, yerel geliştirme ortamında daha ayrıntılı içerik üzerinde anlaşılan çıkış de sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="44089-132">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="44089-133">Geliştirme ve üretim ortamları genelinde tutarlı bir yük biçimi oluşturmak için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="44089-133">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="44089-134">' De `Startup.Configure` , ortama özgü özel durum Işleme ara yazılım örneklerini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="44089-134">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseExceptionHandler("/error-local-development");
        }
        else
        {
            app.UseExceptionHandler("/error");
        }
    }
    ```

    ::: moniker-end

    <span data-ttu-id="44089-135">Yukarıdaki kodda, ara yazılım ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="44089-135">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="44089-136">`/error-local-development`Geliştirme ortamında bir yol.</span><span class="sxs-lookup"><span data-stu-id="44089-136">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="44089-137">`/error`Geliştirme olmayan ortamlarda bir yolu.</span><span class="sxs-lookup"><span data-stu-id="44089-137">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="44089-138">Denetleyici eylemlerine öznitelik yönlendirmeyi Uygula:</span><span class="sxs-lookup"><span data-stu-id="44089-138">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    <span data-ttu-id="44089-139">Yukarıdaki kod, bir yanıt oluşturmak için [ControllerBase. sorun](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) ' yı çağırır <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="44089-139">The preceding code calls [ControllerBase.Problem](xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A) to create a <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> response.</span></span>

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="44089-140">Yanıtı değiştirmek için özel durumları kullanın</span><span class="sxs-lookup"><span data-stu-id="44089-140">Use exceptions to modify the response</span></span>

<span data-ttu-id="44089-141">Yanıtın içeriği, denetleyicinin dışından değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="44089-141">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="44089-142">ASP.NET 4. x Web API 'sinde, bunu yapmanın bir yolu <xref:System.Web.Http.HttpResponseException> türünü kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="44089-142">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="44089-143">ASP.NET Core eşdeğer bir tür içermez.</span><span class="sxs-lookup"><span data-stu-id="44089-143">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="44089-144">İçin destek `HttpResponseException` aşağıdaki adımlarla eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="44089-144">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="44089-145">Adında iyi bilinen bir özel durum türü oluşturun `HttpResponseException` :</span><span class="sxs-lookup"><span data-stu-id="44089-145">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="44089-146">Adlı bir eylem filtresi oluşturun `HttpResponseExceptionFilter` :</span><span class="sxs-lookup"><span data-stu-id="44089-146">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    <span data-ttu-id="44089-147">Önceki filtre, `Order` en büyük tamsayı değeri olan eksi 10 ' u belirtir.</span><span class="sxs-lookup"><span data-stu-id="44089-147">The preceding filter specifies an `Order` of the maximum integer value minus 10.</span></span> <span data-ttu-id="44089-148">Bu, diğer filtrelerin işlem hattının sonunda çalışmasına izin verir.</span><span class="sxs-lookup"><span data-stu-id="44089-148">This allows other filters to run at the end of the pipeline.</span></span>

1. <span data-ttu-id="44089-149">İçinde `Startup.ConfigureServices` , filtre koleksiyonuna eylem filtresini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="44089-149">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="44089-150">Doğrulama hatası hata yanıtı</span><span class="sxs-lookup"><span data-stu-id="44089-150">Validation failure error response</span></span>

<span data-ttu-id="44089-151">Web API denetleyicileri için, bir <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> model doğrulaması başarısız olduğunda MVC bir yanıt türüyle yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="44089-151">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="44089-152">MVC, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> doğrulama hatasına yönelik hata yanıtını oluşturmak için sonuçlarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="44089-152">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="44089-153">Aşağıdaki örnek, varsayılan yanıt türünü içinde olarak değiştirmek için fabrikası kullanır <xref:Microsoft.AspNetCore.Mvc.SerializableError> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44089-153">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="44089-154">İstemci hata yanıtı</span><span class="sxs-lookup"><span data-stu-id="44089-154">Client error response</span></span>

<span data-ttu-id="44089-155">Bir *hata sonucu* , 400 veya ÜZERI bir http durum kodu ile sonuç olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="44089-155">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="44089-156">Web API denetleyicileri için, MVC bir hata sonucunu ile sonucuyla dönüştürür <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="44089-156">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="44089-157">ASP.NET Core 2,1, neredeyse RFC 7807 uyumlu olan bir sorun ayrıntıları yanıtı üretir.</span><span class="sxs-lookup"><span data-stu-id="44089-157">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="44089-158">Yüzde 100 uyumluluğu önemliyse, projeyi ASP.NET Core 2,2 veya üzeri bir sürüme yükseltin.</span><span class="sxs-lookup"><span data-stu-id="44089-158">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44089-159">Hata yanıtı aşağıdaki yollarla yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="44089-159">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="44089-160">Problemayrıntılar Fabrikası Uygulama</span><span class="sxs-lookup"><span data-stu-id="44089-160">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="44089-161">ApiBehaviorOptions. ClientErrorMapping kullanın</span><span class="sxs-lookup"><span data-stu-id="44089-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="44089-162">Uygulamaktır `ProblemDetailsFactory`</span><span class="sxs-lookup"><span data-stu-id="44089-162">Implement `ProblemDetailsFactory`</span></span>

<span data-ttu-id="44089-163">MVC, <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> ve öğesinin tüm örneklerini üretmek için kullanır <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="44089-163">MVC uses <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="44089-164">Buna istemci hata yanıtları, doğrulama hatası hata yanıtları ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> yardımcı yöntemler dahildir.</span><span class="sxs-lookup"><span data-stu-id="44089-164">This includes client error responses, validation failure error responses, and the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> helper methods.</span></span>

<span data-ttu-id="44089-165">Sorun ayrıntıları yanıtını özelleştirmek için, içinde özel bir uygulamasını kaydedin <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="44089-165">To customize the problem details response, register a custom implementation of <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="44089-166">Hata yanıtı, [Use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) bölümünde özetlenen şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="44089-166">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="44089-167">ApiBehaviorOptions. ClientErrorMapping kullanın</span><span class="sxs-lookup"><span data-stu-id="44089-167">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="44089-168"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A>Yanıtın içeriğini yapılandırmak için özelliğini kullanın `ProblemDetails` .</span><span class="sxs-lookup"><span data-stu-id="44089-168">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="44089-169">Örneğin, aşağıdaki kod `Startup.ConfigureServices` `type` 404 yanıtlarının özelliğini güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="44089-169">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end

## <a name="custom-middleware-to-handle-exceptions"></a><span data-ttu-id="44089-170">Özel durumları işlemek için özel ara yazılım</span><span class="sxs-lookup"><span data-stu-id="44089-170">Custom Middleware to handle exceptions</span></span>

<span data-ttu-id="44089-171">Özel durum işleme ara yazılım içindeki varsayılanlar çoğu uygulama için iyi sonuç verir.</span><span class="sxs-lookup"><span data-stu-id="44089-171">The defaults in the exception handling middleware works well for most apps.</span></span> <span data-ttu-id="44089-172">Özel durum işleme gerektiren uygulamalar için [özel durum işleme ara yazılımını özelleştirmeyi](xref:fundamentals/error-handling#exception-handler-lambda)düşünün.</span><span class="sxs-lookup"><span data-stu-id="44089-172">For apps that require specialized exception handling, consider [customizing the exception handling middleware](xref:fundamentals/error-handling#exception-handler-lambda).</span></span>
