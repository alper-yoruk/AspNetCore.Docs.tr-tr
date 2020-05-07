---
title: ASP.NET Core 'de HttpContext 'e erişme
author: coderandhiker
description: ASP.NET Core ' de HttpContext 'e erişme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: 716e74551b95455c99abbac58b712f013acfde56
ms.sourcegitcommit: d4527df91f2c15bbe1cbf5a541adbea5747897aa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82852370"
---
# <a name="access-httpcontext-in-aspnet-core"></a>ASP.NET Core 'de HttpContext 'e erişme

Uygulamalar, `HttpContext` <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> arabirim ve varsayılan uygulaması <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>üzerinden erişim ASP.NET Core. Yalnızca bir hizmetin `HttpContext` içine erişmeniz gerektiğinde `IHttpContextAccessor` kullanılması gerekir.

## <a name="use-httpcontext-from-razor-pages"></a>Razor Sayfalardan HttpContext kullanın

Razor <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> Sayfalar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> özelliği kullanıma sunar:

```csharp
public class AboutModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = HttpContext.Request.PathBase;
    }
}
```

## <a name="use-httpcontext-from-a-razor-view"></a>Bir Razor görünümden HttpContext kullanma

Razorgörünümler, `HttpContext` görünümdeki doğrudan bir [RazorPage. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) özelliği aracılığıyla kullanıma sunar. Aşağıdaki örnek, Windows kimlik doğrulamasını kullanarak bir intranet uygulamasındaki geçerli kullanıcı adını alır:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Bir denetleyiciden HttpContext kullanma

Denetleyiciler [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) özelliğini kullanıma sunar:

```csharp
public class HomeController : Controller
{
    public IActionResult About()
    {
        var pathBase = HttpContext.Request.PathBase;

        ...

        return View();
    }
}
```

## <a name="use-httpcontext-from-middleware"></a>Ara yazılım içinden HttpContext kullanın

Özel ara yazılım bileşenleriyle çalışırken, `HttpContext` `Invoke` veya `InvokeAsync` yöntemine geçirilir ve ara yazılım yapılandırıldığında erişilebilir:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Özel bileşenlerden HttpContext kullanın

İçin `HttpContext`erişim gerektiren diğer Framework ve özel bileşenler için önerilen yaklaşım, yerleşik [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısını kullanarak bir bağımlılığı kaydetmek içindir. Bağımlılık ekleme kapsayıcısı, `IHttpContextAccessor` öğesini oluşturucularının bir bağımlılığı olarak bildiren tüm sınıflara sağlar:

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddControllersWithViews();
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
     services.AddMvc()
         .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
     services.AddHttpContextAccessor();
     services.AddTransient<IUserRepository, UserRepository>();
}
```

::: moniker-end

Aşağıdaki örnekte:

* `UserRepository`bağımlılığını bildirir `IHttpContextAccessor`.
* Bağımlılık ekleme, bağımlılık zincirini çözdüğünde ve bir örneği oluşturduğunda bağımlılık sağlanır `UserRepository`.

```csharp
public class UserRepository : IUserRepository
{
    private readonly IHttpContextAccessor _httpContextAccessor;

    public UserRepository(IHttpContextAccessor httpContextAccessor)
    {
        _httpContextAccessor = httpContextAccessor;
    }

    public void LogCurrentUser()
    {
        var username = _httpContextAccessor.HttpContext.User.Identity.Name;
        service.LogAccessRequest(username);
    }
}
```

## <a name="httpcontext-access-from-a-background-thread"></a>Arka plan iş parçacığından HttpContext erişimi

`HttpContext`iş parçacığı açısından güvenli değildir. Bir isteği işlemenin `HttpContext` dışında okuma veya yazma özellikleri bir <xref:System.NullReferenceException>ile sonuçlanabilir.

> [!NOTE]
> Uygulamanız tek tek, tek tek `NullReferenceException` çoklu hatalar oluşturursa, arka plan işlemesini başlatan veya bir istek tamamlandıktan sonra işlemeye devam eden kodun bölümlerini gözden geçirin. Bir denetleyici metodunu olarak `async void`tanımlama gibi hataları arayın.

`HttpContext` Verilerle arka planda çalışmayı güvenle gerçekleştirmek için:

* İstek işleme sırasında gerekli verileri kopyalayın.
* Kopyalanmış verileri bir arka plan görevine geçirin.

Güvenli olmayan koddan kaçınmak için, arka plan `HttpContext` işini gerçekleştiren bir yönteme hiçbir şekilde geçirmeyin. Bunun yerine gerekli verileri geçirin. Aşağıdaki örnekte, `SendEmailCore` bir e-posta göndermeye başlamak için çağırılır. `correlationId` Öğesine geçirilir, öğesine `SendEmailCore`geçirilir `HttpContext`. Kod yürütme işleminin tamamlanmasını beketmez `SendEmailCore` :

```csharp
public class EmailController : Controller
{
    public IActionResult SendEmail(string email)
    {
        var correlationId = HttpContext.Request.Headers["x-correlation-id"].ToString();

        _ = SendEmailCore(correlationId);

        return View();
    }

    private async Task SendEmailCore(string correlationId)
    {
        ...
    }
}
```

## <a name="blazor-and-shared-state"></a>Blazorve paylaşılan durum

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
