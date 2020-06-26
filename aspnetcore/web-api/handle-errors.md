---
title: ASP.NET Core Web API 'Lerinde hataları işleme
author: pranavkm
description: ASP.NET Core Web API 'Leri ile hata işleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/handle-errors
ms.openlocfilehash: f756d9abfb92fd4d6d51d8762967ac2288b54b2a
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85405763"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a><span data-ttu-id="a8be4-103">ASP.NET Core Web API 'Lerinde hataları işleme</span><span class="sxs-lookup"><span data-stu-id="a8be4-103">Handle errors in ASP.NET Core web APIs</span></span>

<span data-ttu-id="a8be4-104">Bu makalede, ASP.NET Core Web API 'Leriyle hata işlemenin nasıl işleneceği ve özelleştirileceği açıklanır.</span><span class="sxs-lookup"><span data-stu-id="a8be4-104">This article describes how to handle and customize error handling with ASP.NET Core web APIs.</span></span>

<span data-ttu-id="a8be4-105">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="a8be4-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([How to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="a8be4-106">Geliştirici özel durum sayfası</span><span class="sxs-lookup"><span data-stu-id="a8be4-106">Developer Exception Page</span></span>

<span data-ttu-id="a8be4-107">[Geliştirici özel durum sayfası](xref:fundamentals/error-handling) , sunucu hataları için ayrıntılı yığın izlemeleri almak için kullanışlı bir araçtır.</span><span class="sxs-lookup"><span data-stu-id="a8be4-107">The [Developer Exception Page](xref:fundamentals/error-handling) is a useful tool to get detailed stack traces for server errors.</span></span> <span data-ttu-id="a8be4-108">Bu <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> , http ardışık düzeninde zaman uyumlu ve zaman uyumsuz özel durumları yakalamak ve hata yanıtları oluşturmak için kullanır.</span><span class="sxs-lookup"><span data-stu-id="a8be4-108">It uses <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> to capture synchronous and asynchronous exceptions from the HTTP pipeline and to generate error responses.</span></span> <span data-ttu-id="a8be4-109">Göstermek için aşağıdaki denetleyici eylemini göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="a8be4-109">To illustrate, consider the following controller action:</span></span>

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

<span data-ttu-id="a8be4-110">`curl`Önceki eylemi test etmek için aşağıdaki komutu çalıştırın:</span><span class="sxs-lookup"><span data-stu-id="a8be4-110">Run the following `curl` command to test the preceding action:</span></span>

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8be4-111">ASP.NET Core 3,0 ve üzeri sürümlerde geliştirici özel durum sayfasında, istemci HTML biçimli çıkış isteğinde yoksa bir düz metin yanıtı görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-111">In ASP.NET Core 3.0 and later, the Developer Exception Page displays a plain-text response if the client doesn't request HTML-formatted output.</span></span> <span data-ttu-id="a8be4-112">Şu çıktı görünür:</span><span class="sxs-lookup"><span data-stu-id="a8be4-112">The following output appears:</span></span>

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

<span data-ttu-id="a8be4-113">Bunun yerine HTML biçimli bir yanıt göstermek için, `Accept` http istek üst bilgisini `text/html` medya türüne ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="a8be4-113">To display an HTML-formatted response instead, set the `Accept` HTTP request header to the `text/html` media type.</span></span> <span data-ttu-id="a8be4-114">Örneğin:</span><span class="sxs-lookup"><span data-stu-id="a8be4-114">For example:</span></span>

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

<span data-ttu-id="a8be4-115">HTTP yanıtından aşağıdaki alıntıyı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="a8be4-115">Consider the following excerpt from the HTTP response:</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="a8be4-116">ASP.NET Core 2,2 ve önceki sürümlerde, geliştirici özel durum sayfasında HTML biçimli bir yanıt görüntülenir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-116">In ASP.NET Core 2.2 and earlier, the Developer Exception Page displays an HTML-formatted response.</span></span> <span data-ttu-id="a8be4-117">Örneğin, HTTP yanıtından aşağıdaki alıntıyı göz önünde bulundurun:</span><span class="sxs-lookup"><span data-stu-id="a8be4-117">For example, consider the following excerpt from the HTTP response:</span></span>

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

<span data-ttu-id="a8be4-118">HTML biçimli yanıt Postman gibi araçlar aracılığıyla test edilirken yararlı olur.</span><span class="sxs-lookup"><span data-stu-id="a8be4-118">The HTML-formatted response becomes useful when testing via tools like Postman.</span></span> <span data-ttu-id="a8be4-119">Aşağıdaki ekran yakalama, Postman 'daki düz metin ve HTML biçimli yanıtları gösterir:</span><span class="sxs-lookup"><span data-stu-id="a8be4-119">The following screen capture shows both the plain-text and the HTML-formatted responses in Postman:</span></span>

![Geliştirici özel durum sayfası Postman 'da test ediliyor](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> <span data-ttu-id="a8be4-121">Geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken**etkinleştirin.</span><span class="sxs-lookup"><span data-stu-id="a8be4-121">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="a8be4-122">Uygulama üretimde çalıştırıldığında ayrıntılı özel durum bilgilerini herkese açık bir şekilde paylaşmak istemezsiniz.</span><span class="sxs-lookup"><span data-stu-id="a8be4-122">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="a8be4-123">Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..</span><span class="sxs-lookup"><span data-stu-id="a8be4-123">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

## <a name="exception-handler"></a><span data-ttu-id="a8be4-124">Özel durum işleyicisi</span><span class="sxs-lookup"><span data-stu-id="a8be4-124">Exception handler</span></span>

<span data-ttu-id="a8be4-125">Geliştirme dışı ortamlarda, [özel durum Işleme ara yazılımı](xref:fundamentals/error-handling) bir hata yükü oluşturmak için kullanılabilir:</span><span class="sxs-lookup"><span data-stu-id="a8be4-125">In non-development environments, [Exception Handling Middleware](xref:fundamentals/error-handling) can be used to produce an error payload:</span></span>

1. <span data-ttu-id="a8be4-126">' De `Startup.Configure` , <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ara yazılımı kullanmak için çağırın:</span><span class="sxs-lookup"><span data-stu-id="a8be4-126">In `Startup.Configure`, invoke <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> to use the middleware:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. <span data-ttu-id="a8be4-127">Yola yanıt vermek için bir denetleyici eylemi yapılandırın `/error` :</span><span class="sxs-lookup"><span data-stu-id="a8be4-127">Configure a controller action to respond to the `/error` route:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

<span data-ttu-id="a8be4-128">Yukarıdaki `Error` eylem Istemciye [RFC 7807](https://tools.ietf.org/html/rfc7807)ile uyumlu bir yük gönderir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-128">The preceding `Error` action sends an [RFC 7807](https://tools.ietf.org/html/rfc7807)-compliant payload to the client.</span></span>

<span data-ttu-id="a8be4-129">Özel durum Işleme ara yazılımı, yerel geliştirme ortamında daha ayrıntılı içerik üzerinde anlaşılan çıkış de sağlayabilir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-129">Exception Handling Middleware can also provide more detailed content-negotiated output in the local development environment.</span></span> <span data-ttu-id="a8be4-130">Geliştirme ve üretim ortamları genelinde tutarlı bir yük biçimi oluşturmak için aşağıdaki adımları kullanın:</span><span class="sxs-lookup"><span data-stu-id="a8be4-130">Use the following steps to produce a consistent payload format across development and production environments:</span></span>

1. <span data-ttu-id="a8be4-131">' De `Startup.Configure` , ortama özgü özel durum Işleme ara yazılım örneklerini kaydedin:</span><span class="sxs-lookup"><span data-stu-id="a8be4-131">In `Startup.Configure`, register environment-specific Exception Handling Middleware instances:</span></span>

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

    <span data-ttu-id="a8be4-132">Yukarıdaki kodda, ara yazılım ile kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="a8be4-132">In the preceding code, the middleware is registered with:</span></span>

    * <span data-ttu-id="a8be4-133">`/error-local-development`Geliştirme ortamında bir yol.</span><span class="sxs-lookup"><span data-stu-id="a8be4-133">A route of `/error-local-development` in the Development environment.</span></span>
    * <span data-ttu-id="a8be4-134">`/error`Geliştirme olmayan ortamlarda bir yolu.</span><span class="sxs-lookup"><span data-stu-id="a8be4-134">A route of `/error` in environments that aren't Development.</span></span>
    
1. <span data-ttu-id="a8be4-135">Denetleyici eylemlerine öznitelik yönlendirmeyi Uygula:</span><span class="sxs-lookup"><span data-stu-id="a8be4-135">Apply attribute routing to controller actions:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a><span data-ttu-id="a8be4-136">Yanıtı değiştirmek için özel durumları kullanın</span><span class="sxs-lookup"><span data-stu-id="a8be4-136">Use exceptions to modify the response</span></span>

<span data-ttu-id="a8be4-137">Yanıtın içeriği, denetleyicinin dışından değiştirilebilir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-137">The contents of the response can be modified from outside of the controller.</span></span> <span data-ttu-id="a8be4-138">ASP.NET 4. x Web API 'sinde, bunu yapmanın bir yolu <xref:System.Web.Http.HttpResponseException> türünü kullanmaktır.</span><span class="sxs-lookup"><span data-stu-id="a8be4-138">In ASP.NET 4.x Web API, one way to do this was using the <xref:System.Web.Http.HttpResponseException> type.</span></span> <span data-ttu-id="a8be4-139">ASP.NET Core eşdeğer bir tür içermez.</span><span class="sxs-lookup"><span data-stu-id="a8be4-139">ASP.NET Core doesn't include an equivalent type.</span></span> <span data-ttu-id="a8be4-140">İçin destek `HttpResponseException` aşağıdaki adımlarla eklenebilir:</span><span class="sxs-lookup"><span data-stu-id="a8be4-140">Support for `HttpResponseException` can be added with the following steps:</span></span>

1. <span data-ttu-id="a8be4-141">Adında iyi bilinen bir özel durum türü oluşturun `HttpResponseException` :</span><span class="sxs-lookup"><span data-stu-id="a8be4-141">Create a well-known exception type named `HttpResponseException`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. <span data-ttu-id="a8be4-142">Adlı bir eylem filtresi oluşturun `HttpResponseExceptionFilter` :</span><span class="sxs-lookup"><span data-stu-id="a8be4-142">Create an action filter named `HttpResponseExceptionFilter`:</span></span>

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. <span data-ttu-id="a8be4-143">İçinde `Startup.ConfigureServices` , filtre koleksiyonuna eylem filtresini ekleyin:</span><span class="sxs-lookup"><span data-stu-id="a8be4-143">In `Startup.ConfigureServices`, add the action filter to the filters collection:</span></span>

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a><span data-ttu-id="a8be4-144">Doğrulama hatası hata yanıtı</span><span class="sxs-lookup"><span data-stu-id="a8be4-144">Validation failure error response</span></span>

<span data-ttu-id="a8be4-145">Web API denetleyicileri için, bir <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> model doğrulaması başarısız olduğunda MVC bir yanıt türüyle yanıt verir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-145">For web API controllers, MVC responds with a <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> response type when model validation fails.</span></span> <span data-ttu-id="a8be4-146">MVC, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> doğrulama hatasına yönelik hata yanıtını oluşturmak için sonuçlarını kullanır.</span><span class="sxs-lookup"><span data-stu-id="a8be4-146">MVC uses the results of <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> to construct the error response for a validation failure.</span></span> <span data-ttu-id="a8be4-147">Aşağıdaki örnek, varsayılan yanıt türünü içinde olarak değiştirmek için fabrikası kullanır <xref:Microsoft.AspNetCore.Mvc.SerializableError> `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a8be4-147">The following example uses the factory to change the default response type to <xref:Microsoft.AspNetCore.Mvc.SerializableError> in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a><span data-ttu-id="a8be4-148">İstemci hata yanıtı</span><span class="sxs-lookup"><span data-stu-id="a8be4-148">Client error response</span></span>

<span data-ttu-id="a8be4-149">Bir *hata sonucu* , 400 veya ÜZERI bir http durum kodu ile sonuç olarak tanımlanır.</span><span class="sxs-lookup"><span data-stu-id="a8be4-149">An *error result* is defined as a result with an HTTP status code of 400 or higher.</span></span> <span data-ttu-id="a8be4-150">Web API denetleyicileri için, MVC bir hata sonucunu ile sonucuyla dönüştürür <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="a8be4-150">For web API controllers, MVC transforms an error result to a result with <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>.</span></span>

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> <span data-ttu-id="a8be4-151">ASP.NET Core 2,1, neredeyse RFC 7807 uyumlu olan bir sorun ayrıntıları yanıtı üretir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-151">ASP.NET Core 2.1 generates a problem details response that's nearly RFC 7807-compliant.</span></span> <span data-ttu-id="a8be4-152">Yüzde 100 uyumluluğu önemliyse, projeyi ASP.NET Core 2,2 veya üzeri bir sürüme yükseltin.</span><span class="sxs-lookup"><span data-stu-id="a8be4-152">If 100 percent compliance is important, upgrade the project to ASP.NET Core 2.2 or later.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a8be4-153">Hata yanıtı aşağıdaki yollarla yapılandırılabilir:</span><span class="sxs-lookup"><span data-stu-id="a8be4-153">The error response can be configured in one of the following ways:</span></span>

1. [<span data-ttu-id="a8be4-154">Problemayrıntılar Fabrikası Uygulama</span><span class="sxs-lookup"><span data-stu-id="a8be4-154">Implement ProblemDetailsFactory</span></span>](#implement-problemdetailsfactory)
1. [<span data-ttu-id="a8be4-155">ApiBehaviorOptions. ClientErrorMapping kullanın</span><span class="sxs-lookup"><span data-stu-id="a8be4-155">Use ApiBehaviorOptions.ClientErrorMapping</span></span>](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a><span data-ttu-id="a8be4-156">Problemayrıntılar Fabrikası Uygulama</span><span class="sxs-lookup"><span data-stu-id="a8be4-156">Implement ProblemDetailsFactory</span></span>

<span data-ttu-id="a8be4-157">MVC, `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` ve öğesinin tüm örneklerini üretmek için kullanır <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> .</span><span class="sxs-lookup"><span data-stu-id="a8be4-157">MVC uses `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` to produce all instances of <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> and <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>.</span></span> <span data-ttu-id="a8be4-158">Buna istemci hata yanıtları, doğrulama hatası hata yanıtları ve `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> yardımcı yöntemler dahildir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-158">This includes client error responses, validation failure error responses, and the `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` and <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> helper methods.</span></span>

<span data-ttu-id="a8be4-159">Sorun ayrıntıları yanıtını özelleştirmek için, içinde özel bir uygulamasını kaydedin `ProblemDetailsFactory` `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="a8be4-159">To customize the problem details response, register a custom implementation of `ProblemDetailsFactory` in `Startup.ConfigureServices`:</span></span>

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a8be4-160">Hata yanıtı, [Use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) bölümünde özetlenen şekilde yapılandırılabilir.</span><span class="sxs-lookup"><span data-stu-id="a8be4-160">The error response can be configured as outlined in the [Use ApiBehaviorOptions.ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) section.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a><span data-ttu-id="a8be4-161">ApiBehaviorOptions. ClientErrorMapping kullanın</span><span class="sxs-lookup"><span data-stu-id="a8be4-161">Use ApiBehaviorOptions.ClientErrorMapping</span></span>

<span data-ttu-id="a8be4-162"><xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A>Yanıtın içeriğini yapılandırmak için özelliğini kullanın `ProblemDetails` .</span><span class="sxs-lookup"><span data-stu-id="a8be4-162">Use the <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> property to configure the contents of the `ProblemDetails` response.</span></span> <span data-ttu-id="a8be4-163">Örneğin, aşağıdaki kod `Startup.ConfigureServices` `type` 404 yanıtlarının özelliğini güncelleştirir:</span><span class="sxs-lookup"><span data-stu-id="a8be4-163">For example, the following code in `Startup.ConfigureServices` updates the `type` property for 404 responses:</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
