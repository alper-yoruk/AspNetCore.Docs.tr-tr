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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/handle-errors
ms.openlocfilehash: 7c641fb12e0d06ebd7bb3ce9f878f0469b4a3d8e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775069"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>ASP.NET Core Web API 'Lerinde hataları işleme

Bu makalede, ASP.NET Core Web API 'Leriyle hata işlemenin nasıl işleneceği ve özelleştirileceği açıklanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Geliştirici özel durum sayfası

[Geliştirici özel durum sayfası](xref:fundamentals/error-handling) , sunucu hataları için ayrıntılı yığın izlemeleri almak için kullanışlı bir araçtır. Bu, <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> http ardışık düzeninde zaman uyumlu ve zaman uyumsuz özel durumları yakalamak ve hata yanıtları oluşturmak için kullanır. Göstermek için aşağıdaki denetleyici eylemini göz önünde bulundurun:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

Önceki eylemi test `curl` etmek için aşağıdaki komutu çalıştırın:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 3,0 ve üzeri sürümlerde geliştirici özel durum sayfasında, istemci HTML biçimli çıkış isteğinde yoksa bir düz metin yanıtı görüntülenir. Şu çıktı görünür:

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

Bunun yerine HTML biçimli bir yanıt göstermek için, `Accept` http istek üst bilgisini `text/html` medya türüne ayarlayın. Örneğin:

```bash
curl -i -H "Accept: text/html" https://localhost:5001/weatherforecast/chicago
```

HTTP yanıtından aşağıdaki alıntıyı göz önünde bulundurun:

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

ASP.NET Core 2,2 ve önceki sürümlerde, geliştirici özel durum sayfasında HTML biçimli bir yanıt görüntülenir. Örneğin, HTTP yanıtından aşağıdaki alıntıyı göz önünde bulundurun:

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

HTML biçimli yanıt Postman gibi araçlar aracılığıyla test edilirken yararlı olur. Aşağıdaki ekran yakalama, Postman 'daki düz metin ve HTML biçimli yanıtları gösterir:

![Geliştirici özel durum sayfası Postman 'da test ediliyor](handle-errors/_static/developer-exception-page-postman.gif)

::: moniker-end

> [!WARNING]
> Geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken**etkinleştirin. Uygulama üretimde çalıştırıldığında ayrıntılı özel durum bilgilerini herkese açık bir şekilde paylaşmak istemezsiniz. Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments>..

## <a name="exception-handler"></a>Özel durum işleyicisi

Geliştirme dışı ortamlarda, [özel durum Işleme ara yazılımı](xref:fundamentals/error-handling) bir hata yükü oluşturmak için kullanılabilir:

1. ' `Startup.Configure`De, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ara yazılımı kullanmak için çağırın:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. `/error` Yola yanıt vermek için bir denetleyici eylemi yapılandırın:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Yukarıdaki `Error` eylem istemciye [RFC 7807](https://tools.ietf.org/html/rfc7807)ile uyumlu bir yük gönderir.

Özel durum Işleme ara yazılımı, yerel geliştirme ortamında daha ayrıntılı içerik üzerinde anlaşılan çıkış de sağlayabilir. Geliştirme ve üretim ortamları genelinde tutarlı bir yük biçimi oluşturmak için aşağıdaki adımları kullanın:

1. ' `Startup.Configure`De, ortama özgü özel durum Işleme ara yazılım örneklerini kaydedin:

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

    Yukarıdaki kodda, ara yazılım ile kaydedilir:

    * Geliştirme ortamında bir `/error-local-development` yol.
    * Geliştirme olmayan ortamlarda `/error` bir yolu.
    
1. Denetleyici eylemlerine öznitelik yönlendirmeyi Uygula:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>Yanıtı değiştirmek için özel durumları kullanın

Yanıtın içeriği, denetleyicinin dışından değiştirilebilir. ASP.NET 4. x Web API 'sinde, bunu yapmanın bir yolu <xref:System.Web.Http.HttpResponseException> türünü kullanmaktır. ASP.NET Core eşdeğer bir tür içermez. İçin `HttpResponseException` destek aşağıdaki adımlarla eklenebilir:

1. Adında `HttpResponseException`iyi bilinen bir özel durum türü oluşturun:

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Adlı `HttpResponseExceptionFilter`bir eylem filtresi oluşturun:

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

1. İçinde `Startup.ConfigureServices`, filtre koleksiyonuna eylem filtresini ekleyin:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.2"
    
    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.1"

    [!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_AddExceptionFilter)]

    ::: moniker-end

## <a name="validation-failure-error-response"></a>Doğrulama hatası hata yanıtı

Web API denetleyicileri için, bir model doğrulaması başarısız <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> olduğunda MVC bir yanıt türüyle yanıt verir. MVC, doğrulama hatasına yönelik <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> hata yanıtını oluşturmak için sonuçlarını kullanır. Aşağıdaki örnek, varsayılan yanıt türünü <xref:Microsoft.AspNetCore.Mvc.SerializableError> içinde `Startup.ConfigureServices`olarak değiştirmek için fabrikası kullanır:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=4-13)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=5-14)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

[!code-csharp[](handle-errors/samples/2.x/2.1/Startup.cs?name=snippet_DisableProblemDetailsInvalidModelStateResponseFactory&highlight=6-15)]

::: moniker-end

## <a name="client-error-response"></a>İstemci hata yanıtı

Bir *hata sonucu* , 400 veya ÜZERI bir http durum kodu ile sonuç olarak tanımlanır. Web API denetleyicileri için, MVC bir hata sonucunu ile <xref:Microsoft.AspNetCore.Mvc.ProblemDetails>sonucuyla dönüştürür.

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2,1, neredeyse RFC 7807 uyumlu olan bir sorun ayrıntıları yanıtı üretir. Yüzde 100 uyumluluğu önemliyse, projeyi ASP.NET Core 2,2 veya üzeri bir sürüme yükseltin.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Hata yanıtı aşağıdaki yollarla yapılandırılabilir:

1. [Problemayrıntılar Fabrikası Uygulama](#implement-problemdetailsfactory)
1. [ApiBehaviorOptions. ClientErrorMapping kullanın](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Problemayrıntılar Fabrikası Uygulama

MVC, `Microsoft.AspNetCore.Mvc.ProblemDetailsFactory` ve <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails>öğesinin tüm örneklerini üretmek için kullanır. Buna istemci hata yanıtları, doğrulama hatası hata yanıtları ve `Microsoft.AspNetCore.Mvc.ControllerBase.Problem` ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem> yardımcı yöntemler dahildir.

Sorun ayrıntıları yanıtını özelleştirmek için, `ProblemDetailsFactory` içinde `Startup.ConfigureServices`özel bir uygulamasını kaydedin:

```csharp
public void ConfigureServices(IServiceCollection serviceCollection)
{
    services.AddControllers();
    services.AddTransient<ProblemDetailsFactory, CustomProblemDetailsFactory>();
}
```

::: moniker-end

::: moniker range="= aspnetcore-2.2"

Hata yanıtı, [Use ApiBehaviorOptions. ClientErrorMapping](#use-apibehavioroptionsclienterrormapping) bölümünde özetlenen şekilde yapılandırılabilir.

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="use-apibehavioroptionsclienterrormapping"></a>ApiBehaviorOptions. ClientErrorMapping kullanın

`ProblemDetails` Yanıtın içeriğini yapılandırmak için <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A> özelliğini kullanın. Örneğin, aşağıdaki kod 404 yanıtlarının `Startup.ConfigureServices` `type` özelliğini güncelleştirir:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
