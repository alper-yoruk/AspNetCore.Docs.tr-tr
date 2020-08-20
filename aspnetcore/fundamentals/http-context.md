---
title: ASP.NET Core 'de HttpContext 'e erişme
author: coderandhiker
description: ASP.NET Core ' de HttpContext 'e erişme hakkında bilgi edinin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/5/2020
no-loc:
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
uid: fundamentals/httpcontext
ms.openlocfilehash: 0eade76f8cf0bdd81cc290218f36fe9276233104
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88635287"
---
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="691a9-103">ASP.NET Core 'de HttpContext 'e erişme</span><span class="sxs-lookup"><span data-stu-id="691a9-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="691a9-104">Uygulamalar `HttpContext` , <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> arabirim ve varsayılan uygulaması üzerinden erişim ASP.NET Core <xref:Microsoft.AspNetCore.Http.HttpContextAccessor> .</span><span class="sxs-lookup"><span data-stu-id="691a9-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="691a9-105">Yalnızca `IHttpContextAccessor` bir hizmetin içine erişmeniz gerektiğinde kullanılması gerekir `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="691a9-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-no-locrazor-pages"></a><span data-ttu-id="691a9-106">Sayfalardan HttpContext kullanın Razor</span><span class="sxs-lookup"><span data-stu-id="691a9-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="691a9-107">RazorSayfalar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> özelliği kullanıma sunar <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> :</span><span class="sxs-lookup"><span data-stu-id="691a9-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-no-locrazor-view"></a><span data-ttu-id="691a9-108">Bir görünümden HttpContext kullanma Razor</span><span class="sxs-lookup"><span data-stu-id="691a9-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="691a9-109">Razorgörünümler, `HttpContext` görünümdeki bir [ Razor Page. Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) özelliği aracılığıyla doğrudan kullanıma sunar.</span><span class="sxs-lookup"><span data-stu-id="691a9-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="691a9-110">Aşağıdaki örnek, Windows kimlik doğrulamasını kullanarak bir intranet uygulamasındaki geçerli kullanıcı adını alır:</span><span class="sxs-lookup"><span data-stu-id="691a9-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="691a9-111">Bir denetleyiciden HttpContext kullanma</span><span class="sxs-lookup"><span data-stu-id="691a9-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="691a9-112">Denetleyiciler [ControllerBase. HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) özelliğini kullanıma sunar:</span><span class="sxs-lookup"><span data-stu-id="691a9-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="691a9-113">Ara yazılım içinden HttpContext kullanın</span><span class="sxs-lookup"><span data-stu-id="691a9-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="691a9-114">Özel ara yazılım bileşenleriyle çalışırken, `HttpContext` `Invoke` veya `InvokeAsync` yöntemine geçirilir ve ara yazılım yapılandırıldığında erişilebilir:</span><span class="sxs-lookup"><span data-stu-id="691a9-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="691a9-115">Özel bileşenlerden HttpContext kullanın</span><span class="sxs-lookup"><span data-stu-id="691a9-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="691a9-116">İçin erişim gerektiren diğer Framework ve özel bileşenler için `HttpContext` Önerilen yaklaşım, yerleşik [bağımlılık ekleme](xref:fundamentals/dependency-injection) kapsayıcısını kullanarak bir bağımlılığı kaydetmek içindir.</span><span class="sxs-lookup"><span data-stu-id="691a9-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="691a9-117">Bağımlılık ekleme kapsayıcısı, öğesini `IHttpContextAccessor` oluşturucularının bir bağımlılığı olarak bildiren tüm sınıflara sağlar:</span><span class="sxs-lookup"><span data-stu-id="691a9-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="691a9-118">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="691a9-118">In the following example:</span></span>

* <span data-ttu-id="691a9-119">`UserRepository` bağımlılığını bildirir `IHttpContextAccessor` .</span><span class="sxs-lookup"><span data-stu-id="691a9-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="691a9-120">Bağımlılık ekleme, bağımlılık zincirini çözdüğünde ve bir örneği oluşturduğunda bağımlılık sağlanır `UserRepository` .</span><span class="sxs-lookup"><span data-stu-id="691a9-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="691a9-121">Arka plan iş parçacığından HttpContext erişimi</span><span class="sxs-lookup"><span data-stu-id="691a9-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="691a9-122">`HttpContext` iş parçacığı açısından güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="691a9-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="691a9-123">Bir isteği işlemenin dışında okuma veya yazma özellikleri `HttpContext` bir ile sonuçlanabilir <xref:System.NullReferenceException> .</span><span class="sxs-lookup"><span data-stu-id="691a9-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="691a9-124">Uygulamanız tek tek, tek tek `NullReferenceException` Çoklu hatalar oluşturursa, arka plan işlemesini başlatan veya bir istek tamamlandıktan sonra işlemeye devam eden kodun bölümlerini gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="691a9-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="691a9-125">Bir denetleyici metodunu olarak tanımlama gibi hataları arayın `async void` .</span><span class="sxs-lookup"><span data-stu-id="691a9-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="691a9-126">Verilerle arka planda çalışmayı güvenle gerçekleştirmek için `HttpContext` :</span><span class="sxs-lookup"><span data-stu-id="691a9-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="691a9-127">İstek işleme sırasında gerekli verileri kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="691a9-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="691a9-128">Kopyalanmış verileri bir arka plan görevine geçirin.</span><span class="sxs-lookup"><span data-stu-id="691a9-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="691a9-129">Güvenli olmayan koddan kaçınmak için, `HttpContext` arka plan işini gerçekleştiren bir yönteme hiçbir şekilde geçirmeyin.</span><span class="sxs-lookup"><span data-stu-id="691a9-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="691a9-130">Bunun yerine gerekli verileri geçirin.</span><span class="sxs-lookup"><span data-stu-id="691a9-130">Pass the required data instead.</span></span> <span data-ttu-id="691a9-131">Aşağıdaki örnekte, `SendEmailCore` bir e-posta göndermeye başlamak için çağırılır.</span><span class="sxs-lookup"><span data-stu-id="691a9-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="691a9-132">`correlationId`Öğesine geçirilir, öğesine geçirilir `SendEmailCore` `HttpContext` .</span><span class="sxs-lookup"><span data-stu-id="691a9-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="691a9-133">Kod yürütme işleminin tamamlanmasını beketmez `SendEmailCore` :</span><span class="sxs-lookup"><span data-stu-id="691a9-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="691a9-134">Blazor ve paylaşılan durum</span><span class="sxs-lookup"><span data-stu-id="691a9-134">Blazor and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]
