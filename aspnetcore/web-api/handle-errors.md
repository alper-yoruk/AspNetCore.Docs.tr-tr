---
title: ASP.NET Core Web API 'Lerinde hataları işleme
author: pranavkm
description: ASP.NET Core Web API 'Leri ile hata işleme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: prkrishn
ms.custom: mvc
ms.date: 07/23/2020
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
ms.openlocfilehash: 0efcf1bbeeb65cf7f4420f8c50fb4adf7d1d016d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052532"
---
# <a name="handle-errors-in-aspnet-core-web-apis"></a>ASP.NET Core Web API 'Lerinde hataları işleme

Bu makalede, ASP.NET Core Web API 'Leriyle hata işlemenin nasıl işleneceği ve özelleştirileceği açıklanır.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/handle-errors/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

## <a name="developer-exception-page"></a>Geliştirici özel durum sayfası

[Geliştirici özel durum sayfası](xref:fundamentals/error-handling) , sunucu hataları için ayrıntılı yığın izlemeleri almak için kullanışlı bir araçtır. Bu <xref:Microsoft.AspNetCore.Diagnostics.DeveloperExceptionPageMiddleware> , http ardışık düzeninde zaman uyumlu ve zaman uyumsuz özel durumları yakalamak ve hata yanıtları oluşturmak için kullanır. Göstermek için aşağıdaki denetleyici eylemini göz önünde bulundurun:

[!code-csharp[](handle-errors/samples/3.x/Controllers/WeatherForecastController.cs?name=snippet_GetByCity)]

`curl`Önceki eylemi test etmek için aşağıdaki komutu çalıştırın:

```bash
curl -i https://localhost:5001/weatherforecast/chicago
```

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 3,0 ve üzeri sürümlerde geliştirici özel durum sayfasında, istemci HTML biçimli çıkış isteğinde yoksa bir düz metin yanıtı görüntülenir. Aşağıdaki çıkış görüntülenir:

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
> Geliştirici özel durum sayfasını **yalnızca uygulama geliştirme ortamında çalışırken** etkinleştirin. Uygulama üretimde çalıştırıldığında ayrıntılı özel durum bilgilerini herkese açık bir şekilde paylaşmak istemezsiniz. Ortamları yapılandırma hakkında daha fazla bilgi için bkz <xref:fundamentals/environments> ..

## <a name="exception-handler"></a>Özel durum işleyicisi

Geliştirme dışı ortamlarda, [özel durum Işleme ara yazılımı](xref:fundamentals/error-handling) bir hata yükü oluşturmak için kullanılabilir:

1. ' De `Startup.Configure` , <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> ara yazılımı kullanmak için çağırın:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Startup.cs?name=snippet_UseExceptionHandler&highlight=9)]

    ::: moniker-end

1. Yola yanıt vermek için bir denetleyici eylemi yapılandırın `/error` :

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorController)]

    ::: moniker-end

Yukarıdaki `Error` eylem Istemciye [RFC 7807](https://tools.ietf.org/html/rfc7807)ile uyumlu bir yük gönderir.

Özel durum Işleme ara yazılımı, yerel geliştirme ortamında daha ayrıntılı içerik üzerinde anlaşılan çıkış de sağlayabilir. Geliştirme ve üretim ortamları genelinde tutarlı bir yük biçimi oluşturmak için aşağıdaki adımları kullanın:

1. ' De `Startup.Configure` , ortama özgü özel durum Işleme ara yazılım örneklerini kaydedin:

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

    * `/error-local-development`Geliştirme ortamında bir yol.
    * `/error`Geliştirme olmayan ortamlarda bir yolu.
    
1. Denetleyici eylemlerine öznitelik yönlendirmeyi Uygula:

    ::: moniker range=">= aspnetcore-3.0"

    [!code-csharp[](handle-errors/samples/3.x/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

    ::: moniker range="<= aspnetcore-2.2"

    [!code-csharp[](handle-errors/samples/2.x/2.2/Controllers/ErrorController.cs?name=snippet_ErrorControllerEnvironmentSpecific)]

    ::: moniker-end

## <a name="use-exceptions-to-modify-the-response"></a>Yanıtı değiştirmek için özel durumları kullanın

Yanıtın içeriği, denetleyicinin dışından değiştirilebilir. ASP.NET 4. x Web API 'sinde, bunu yapmanın bir yolu <xref:System.Web.Http.HttpResponseException> türünü kullanmaktır. ASP.NET Core eşdeğer bir tür içermez. İçin destek `HttpResponseException` aşağıdaki adımlarla eklenebilir:

1. Adında iyi bilinen bir özel durum türü oluşturun `HttpResponseException` :

    [!code-csharp[](handle-errors/samples/3.x/Exceptions/HttpResponseException.cs?name=snippet_HttpResponseException)]

1. Adlı bir eylem filtresi oluşturun `HttpResponseExceptionFilter` :

    [!code-csharp[](handle-errors/samples/3.x/Filters/HttpResponseExceptionFilter.cs?name=snippet_HttpResponseExceptionFilter)]

    Yukarıdaki filtrede, sihirli sayı 10 en büyük tamsayı değerinden çıkarılır. Bu numarayı çıkarmak, diğer filtrelerin işlem hattının çok sonunda çalışmasına izin verir.

1. İçinde `Startup.ConfigureServices` , filtre koleksiyonuna eylem filtresini ekleyin:

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

Web API denetleyicileri için, bir <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> model doğrulaması başarısız olduğunda MVC bir yanıt türüyle yanıt verir. MVC, <xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.InvalidModelStateResponseFactory> doğrulama hatasına yönelik hata yanıtını oluşturmak için sonuçlarını kullanır. Aşağıdaki örnek, varsayılan yanıt türünü içinde olarak değiştirmek için fabrikası kullanır <xref:Microsoft.AspNetCore.Mvc.SerializableError> `Startup.ConfigureServices` :

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

Bir *hata sonucu* , 400 veya ÜZERI bir http durum kodu ile sonuç olarak tanımlanır. Web API denetleyicileri için, MVC bir hata sonucunu ile sonucuyla dönüştürür <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> .

::: moniker range="= aspnetcore-2.1"

> [!IMPORTANT]
> ASP.NET Core 2,1, neredeyse RFC 7807 uyumlu olan bir sorun ayrıntıları yanıtı üretir. Yüzde 100 uyumluluğu önemliyse, projeyi ASP.NET Core 2,2 veya üzeri bir sürüme yükseltin.

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

Hata yanıtı aşağıdaki yollarla yapılandırılabilir:

1. [Problemayrıntılar Fabrikası Uygulama](#implement-problemdetailsfactory)
1. [ApiBehaviorOptions. ClientErrorMapping kullanın](#use-apibehavioroptionsclienterrormapping)

### <a name="implement-problemdetailsfactory"></a>Uygulamaktır `ProblemDetailsFactory`

MVC, <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory?displayProperty=fullName> ve öğesinin tüm örneklerini üretmek için kullanır <xref:Microsoft.AspNetCore.Mvc.ProblemDetails> <xref:Microsoft.AspNetCore.Mvc.ValidationProblemDetails> . Buna istemci hata yanıtları, doğrulama hatası hata yanıtları ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Problem%2A?displayProperty=nameWithType> ve <xref:Microsoft.AspNetCore.Mvc.ControllerBase.ValidationProblem%2A?displayProperty=nameWithType> yardımcı yöntemler dahildir.

Sorun ayrıntıları yanıtını özelleştirmek için, içinde özel bir uygulamasını kaydedin <xref:Microsoft.AspNetCore.Mvc.Infrastructure.ProblemDetailsFactory> `Startup.ConfigureServices` :

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

<xref:Microsoft.AspNetCore.Mvc.ApiBehaviorOptions.ClientErrorMapping%2A>Yanıtın içeriğini yapılandırmak için özelliğini kullanın `ProblemDetails` . Örneğin, aşağıdaki kod `Startup.ConfigureServices` `type` 404 yanıtlarının özelliğini güncelleştirir:

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=8-9)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

[!code-csharp[](index/samples/2.x/2.2/Startup.cs?name=snippet_ConfigureApiBehaviorOptions&highlight=9-10)]

::: moniker-end
