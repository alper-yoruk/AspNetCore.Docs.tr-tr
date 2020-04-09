---
title: ASP.NET Core'da HttpContext'a Erişin
author: coderandhiker
description: ASP.NET Core'da HttpContext'a nasıl erişilenleri öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/03/2019
uid: fundamentals/httpcontext
ms.openlocfilehash: 8a7ee180380c42ea745c91b8e6a18c1baa820220
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78658748"
---
# <a name="access-httpcontext-in-aspnet-core"></a>ASP.NET Core'da HttpContext'a Erişin

ASP.NET Core `HttpContext` uygulamaları <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> arayüz ve varsayılan <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>uygulama üzerinden erişin. Yalnızca bir hizmetin `IHttpContextAccessor` içinden `HttpContext` erişmeniz gerektiğinde kullanmanız gerekir.

## <a name="use-httpcontext-from-razor-pages"></a>Razor Sayfalarından HttpContext'ı Kullanma

Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> özelliği ni ortaya çıkarır:

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

## <a name="use-httpcontext-from-a-razor-view"></a>Ustura görünümünden HttpContext'ı kullanma

Jilet görünümleri doğrudan bir `HttpContext` [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) özelliği üzerinden görünümünde ortaya koymaktadır. Aşağıdaki örnek, Windows Kimlik Doğrulaması'nı kullanarak bir intranet uygulamasında geçerli kullanıcı adını alır:

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a>Denetleyiciden HttpContext'ı kullanma

Denetleyiciler [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) özelliğini ortaya çıkarır:

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

## <a name="use-httpcontext-from-middleware"></a>Middleware gelen HttpContext kullanın

Özel ara yazılım bileşenleriyle `HttpContext` çalışırken, `Invoke` yönteme `InvokeAsync` aktarılır ve ara yazılım yapılandırıldığında erişilebilir:

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a>Özel bileşenlerden HttpContext'ı kullanma

Erişim gerektiren diğer çerçeve ve `HttpContext`özel bileşenler için, önerilen yaklaşım yerleşik bağımlılık [enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısını kullanarak bir bağımlılık kaydetmektir. Bağımlılık enjeksiyon konteyneri, `IHttpContextAccessor` bunu yapıcılarında bağımlılık olarak beyan eden tüm sınıflara sağlar:

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

* `UserRepository`bağımlılığını `IHttpContextAccessor`bildirir.
* Bağımlılık enjeksiyonu bağımlılık zincirini çözdüğünde ve bir .' nin `UserRepository`örneğini oluşturduğunda bağımlılık sağlanır.

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

## <a name="httpcontext-access-from-a-background-thread"></a>Bir arka plan iş parçacığından bağlam erişimi

`HttpContext`iş parçacığı güvenli değildir. Bir isteği işleme `HttpContext` dışında okuma veya yazma özellikleri <xref:System.NullReferenceException>bir neden olabilir .

> [!NOTE]
> Uygulamanız düzensiz `NullReferenceException` hatalar oluşturuyorsa, kodun arka plan işlemeyi başlatan veya istek tamamlandıktan sonra işlemeye devam eden bölümlerini gözden geçirin. Denetleyici yöntemini ' olarak `async void`tanımlamak gibi hatalara bakın.

Verilerle `HttpContext` arka plan çalışmasını güvenli bir şekilde gerçekleştirmek için:

* İstek işleme sırasında gerekli verileri kopyalayın.
* Kopyalanan verileri arka plan görevine aktarın.

Güvenli olmayan kodlardan kaçınmak `HttpContext` için, arka plan çalışmasını gerçekleştiren bir yönteme geçmeyin. Bunun yerine gerekli verileri geçirin. Aşağıdaki örnekte, `SendEmailCore` e-posta göndermeye başlamak için çağrılır. `SendEmailCore`Geçer, `correlationId` `HttpContext`değil. Kod yürütme tamamlanması nı `SendEmailCore` beklemez:

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
