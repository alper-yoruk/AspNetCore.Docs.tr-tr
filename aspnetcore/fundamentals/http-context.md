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
# <a name="access-httpcontext-in-aspnet-core"></a><span data-ttu-id="a0aff-103">ASP.NET Core'da HttpContext'a Erişin</span><span class="sxs-lookup"><span data-stu-id="a0aff-103">Access HttpContext in ASP.NET Core</span></span>

<span data-ttu-id="a0aff-104">ASP.NET Core `HttpContext` uygulamaları <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> arayüz ve varsayılan <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>uygulama üzerinden erişin.</span><span class="sxs-lookup"><span data-stu-id="a0aff-104">ASP.NET Core apps access `HttpContext` through the <xref:Microsoft.AspNetCore.Http.IHttpContextAccessor> interface and its default implementation <xref:Microsoft.AspNetCore.Http.HttpContextAccessor>.</span></span> <span data-ttu-id="a0aff-105">Yalnızca bir hizmetin `IHttpContextAccessor` içinden `HttpContext` erişmeniz gerektiğinde kullanmanız gerekir.</span><span class="sxs-lookup"><span data-stu-id="a0aff-105">It's only necessary to use `IHttpContextAccessor` when you need access to the `HttpContext` inside a service.</span></span>

## <a name="use-httpcontext-from-razor-pages"></a><span data-ttu-id="a0aff-106">Razor Sayfalarından HttpContext'ı Kullanma</span><span class="sxs-lookup"><span data-stu-id="a0aff-106">Use HttpContext from Razor Pages</span></span>

<span data-ttu-id="a0aff-107">Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> özelliği ni ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="a0aff-107">The Razor Pages <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> exposes the <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> property:</span></span>

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

## <a name="use-httpcontext-from-a-razor-view"></a><span data-ttu-id="a0aff-108">Ustura görünümünden HttpContext'ı kullanma</span><span class="sxs-lookup"><span data-stu-id="a0aff-108">Use HttpContext from a Razor view</span></span>

<span data-ttu-id="a0aff-109">Jilet görünümleri doğrudan bir `HttpContext` [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) özelliği üzerinden görünümünde ortaya koymaktadır.</span><span class="sxs-lookup"><span data-stu-id="a0aff-109">Razor views expose the `HttpContext` directly via a [RazorPage.Context](xref:Microsoft.AspNetCore.Mvc.Razor.RazorPage.Context) property on the view.</span></span> <span data-ttu-id="a0aff-110">Aşağıdaki örnek, Windows Kimlik Doğrulaması'nı kullanarak bir intranet uygulamasında geçerli kullanıcı adını alır:</span><span class="sxs-lookup"><span data-stu-id="a0aff-110">The following example retrieves the current username in an intranet app using Windows Authentication:</span></span>

```cshtml
@{
    var username = Context.User.Identity.Name;
    
    ...
}
```

## <a name="use-httpcontext-from-a-controller"></a><span data-ttu-id="a0aff-111">Denetleyiciden HttpContext'ı kullanma</span><span class="sxs-lookup"><span data-stu-id="a0aff-111">Use HttpContext from a controller</span></span>

<span data-ttu-id="a0aff-112">Denetleyiciler [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) özelliğini ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="a0aff-112">Controllers expose the [ControllerBase.HttpContext](xref:Microsoft.AspNetCore.Mvc.ControllerBase.HttpContext) property:</span></span>

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

## <a name="use-httpcontext-from-middleware"></a><span data-ttu-id="a0aff-113">Middleware gelen HttpContext kullanın</span><span class="sxs-lookup"><span data-stu-id="a0aff-113">Use HttpContext from middleware</span></span>

<span data-ttu-id="a0aff-114">Özel ara yazılım bileşenleriyle `HttpContext` çalışırken, `Invoke` yönteme `InvokeAsync` aktarılır ve ara yazılım yapılandırıldığında erişilebilir:</span><span class="sxs-lookup"><span data-stu-id="a0aff-114">When working with custom middleware components, `HttpContext` is passed into the `Invoke` or `InvokeAsync` method and can be accessed when the middleware is configured:</span></span>

```csharp
public class MyCustomMiddleware
{
    public Task InvokeAsync(HttpContext context)
    {
        ...
    }
}
```

## <a name="use-httpcontext-from-custom-components"></a><span data-ttu-id="a0aff-115">Özel bileşenlerden HttpContext'ı kullanma</span><span class="sxs-lookup"><span data-stu-id="a0aff-115">Use HttpContext from custom components</span></span>

<span data-ttu-id="a0aff-116">Erişim gerektiren diğer çerçeve ve `HttpContext`özel bileşenler için, önerilen yaklaşım yerleşik bağımlılık [enjeksiyon](xref:fundamentals/dependency-injection) kapsayıcısını kullanarak bir bağımlılık kaydetmektir.</span><span class="sxs-lookup"><span data-stu-id="a0aff-116">For other framework and custom components that require access to `HttpContext`, the recommended approach is to register a dependency using the built-in [dependency injection](xref:fundamentals/dependency-injection) container.</span></span> <span data-ttu-id="a0aff-117">Bağımlılık enjeksiyon konteyneri, `IHttpContextAccessor` bunu yapıcılarında bağımlılık olarak beyan eden tüm sınıflara sağlar:</span><span class="sxs-lookup"><span data-stu-id="a0aff-117">The dependency injection container supplies the `IHttpContextAccessor` to any classes that declare it as a dependency in their constructors:</span></span>

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

<span data-ttu-id="a0aff-118">Aşağıdaki örnekte:</span><span class="sxs-lookup"><span data-stu-id="a0aff-118">In the following example:</span></span>

* <span data-ttu-id="a0aff-119">`UserRepository`bağımlılığını `IHttpContextAccessor`bildirir.</span><span class="sxs-lookup"><span data-stu-id="a0aff-119">`UserRepository` declares its dependency on `IHttpContextAccessor`.</span></span>
* <span data-ttu-id="a0aff-120">Bağımlılık enjeksiyonu bağımlılık zincirini çözdüğünde ve bir .' nin `UserRepository`örneğini oluşturduğunda bağımlılık sağlanır.</span><span class="sxs-lookup"><span data-stu-id="a0aff-120">The dependency is supplied when dependency injection resolves the dependency chain and creates an instance of `UserRepository`.</span></span>

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

## <a name="httpcontext-access-from-a-background-thread"></a><span data-ttu-id="a0aff-121">Bir arka plan iş parçacığından bağlam erişimi</span><span class="sxs-lookup"><span data-stu-id="a0aff-121">HttpContext access from a background thread</span></span>

<span data-ttu-id="a0aff-122">`HttpContext`iş parçacığı güvenli değildir.</span><span class="sxs-lookup"><span data-stu-id="a0aff-122">`HttpContext` isn't thread-safe.</span></span> <span data-ttu-id="a0aff-123">Bir isteği işleme `HttpContext` dışında okuma veya yazma özellikleri <xref:System.NullReferenceException>bir neden olabilir .</span><span class="sxs-lookup"><span data-stu-id="a0aff-123">Reading or writing properties of the `HttpContext` outside of processing a request can result in a <xref:System.NullReferenceException>.</span></span>

> [!NOTE]
> <span data-ttu-id="a0aff-124">Uygulamanız düzensiz `NullReferenceException` hatalar oluşturuyorsa, kodun arka plan işlemeyi başlatan veya istek tamamlandıktan sonra işlemeye devam eden bölümlerini gözden geçirin.</span><span class="sxs-lookup"><span data-stu-id="a0aff-124">If your app generates sporadic `NullReferenceException` errors, review parts of the code that start background processing or that continue processing after a request completes.</span></span> <span data-ttu-id="a0aff-125">Denetleyici yöntemini ' olarak `async void`tanımlamak gibi hatalara bakın.</span><span class="sxs-lookup"><span data-stu-id="a0aff-125">Look for mistakes, such as defining a controller method as `async void`.</span></span>

<span data-ttu-id="a0aff-126">Verilerle `HttpContext` arka plan çalışmasını güvenli bir şekilde gerçekleştirmek için:</span><span class="sxs-lookup"><span data-stu-id="a0aff-126">To safely perform background work with `HttpContext` data:</span></span>

* <span data-ttu-id="a0aff-127">İstek işleme sırasında gerekli verileri kopyalayın.</span><span class="sxs-lookup"><span data-stu-id="a0aff-127">Copy the required data during request processing.</span></span>
* <span data-ttu-id="a0aff-128">Kopyalanan verileri arka plan görevine aktarın.</span><span class="sxs-lookup"><span data-stu-id="a0aff-128">Pass the copied data to a background task.</span></span>

<span data-ttu-id="a0aff-129">Güvenli olmayan kodlardan kaçınmak `HttpContext` için, arka plan çalışmasını gerçekleştiren bir yönteme geçmeyin.</span><span class="sxs-lookup"><span data-stu-id="a0aff-129">To avoid unsafe code, never pass the `HttpContext` into a method that performs background work.</span></span> <span data-ttu-id="a0aff-130">Bunun yerine gerekli verileri geçirin.</span><span class="sxs-lookup"><span data-stu-id="a0aff-130">Pass the required data instead.</span></span> <span data-ttu-id="a0aff-131">Aşağıdaki örnekte, `SendEmailCore` e-posta göndermeye başlamak için çağrılır.</span><span class="sxs-lookup"><span data-stu-id="a0aff-131">In the following example, `SendEmailCore` is called to start sending an email.</span></span> <span data-ttu-id="a0aff-132">`SendEmailCore`Geçer, `correlationId` `HttpContext`değil.</span><span class="sxs-lookup"><span data-stu-id="a0aff-132">The `correlationId` is passed to `SendEmailCore`, not the `HttpContext`.</span></span> <span data-ttu-id="a0aff-133">Kod yürütme tamamlanması nı `SendEmailCore` beklemez:</span><span class="sxs-lookup"><span data-stu-id="a0aff-133">Code execution doesn't wait for `SendEmailCore` to complete:</span></span>

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
