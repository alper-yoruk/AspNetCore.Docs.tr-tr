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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/httpcontext
ms.openlocfilehash: d4512c9fa136e518fa0230c0cf9c607519eed6d8
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85399458"
---
# <a name="access-httpcontext-in-aspnet-core"></a>ASP.NET Core 'de HttpContext 'e erişme

Uygulamalar `HttpContext` , <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> arabirim ve varsayılan uygulaması üzerinden erişim ASP.NET Core <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> . Yalnızca `IHttpContextAccessor` bir hizmetin içine erişmeniz gerektiğinde kullanılması gerekir `HttpContext` .

## <a name="use-httpcontext-from-razor-pages"></a>Sayfalardan HttpContext kullanın Razor

RazorSayfalar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> özelliği kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> :

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

## <a name="use-httpcontext-from-a-razor-view"></a>Bir görünümden HttpContext kullanma Razor

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

İçin erişim gerektiren diğer Framework ve özel bileşenler için `HttpContext` Önerilen yaklaşım, yerleşik [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısını kullanarak bir bağımlılığı kaydetmek içindir. Bağımlılık ekleme kapsayıcısı, öğesini `IHttpContextAccessor` oluşturucularının bir bağımlılığı olarak bildiren tüm sınıflara sağlar:

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

* `UserRepository`bağımlılığını bildirir `IHttpContextAccessor` .
* Bağımlılık ekleme, bağımlılık zincirini çözdüğünde ve bir örneği oluşturduğunda bağımlılık sağlanır `UserRepository` .

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

`HttpContext`iş parçacığı açısından güvenli değildir. Bir isteği işlemenin dışında okuma veya yazma özellikleri `HttpContext` bir ile sonuçlanabilir <xref:System.NullReferenceException> .

> [!NOTE]
> Uygulamanız tek tek, tek tek `NullReferenceException` Çoklu hatalar oluşturursa, arka plan işlemesini başlatan veya bir istek tamamlandıktan sonra işlemeye devam eden kodun bölümlerini gözden geçirin. Bir denetleyici metodunu olarak tanımlama gibi hataları arayın `async void` .

Verilerle arka planda çalışmayı güvenle gerçekleştirmek için `HttpContext` :

* İstek işleme sırasında gerekli verileri kopyalayın.
* Kopyalanmış verileri bir arka plan görevine geçirin.

Güvenli olmayan koddan kaçınmak için, `HttpContext` arka plan işini gerçekleştiren bir yönteme hiçbir şekilde geçirmeyin. Bunun yerine gerekli verileri geçirin. Aşağıdaki örnekte, `SendEmailCore` bir e-posta göndermeye başlamak için çağırılır. `correlationId`Öğesine geçirilir, öğesine geçirilir `SendEmailCore` `HttpContext` . Kod yürütme işleminin tamamlanmasını beketmez `SendEmailCore` :

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
