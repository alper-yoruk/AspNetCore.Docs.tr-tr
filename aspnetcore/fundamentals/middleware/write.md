---
title: Özel ASP.NET Core middleware yazın
author: rick-anderson
description: Özel ASP.NET Core middleware yazmayı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: e74bba9e1bd826d4f493b0ee642a198f984daada
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663928"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="5e617-103">Özel ASP.NET Core middleware yazın</span><span class="sxs-lookup"><span data-stu-id="5e617-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="5e617-104">Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="5e617-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="5e617-105">Middleware, istek ve yanıtları işlemek için bir uygulama ardışık hattında bir araya getirilen yazılımdır.</span><span class="sxs-lookup"><span data-stu-id="5e617-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="5e617-106">ASP.NET Core, zengin bir yerleşik ara yazılım bileşeni kümesi sağlar, ancak bazı senaryolarda özel bir ara yazılım yazmak isteyebilirsiniz.</span><span class="sxs-lookup"><span data-stu-id="5e617-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="5e617-107">Ara yazılım sınıfı</span><span class="sxs-lookup"><span data-stu-id="5e617-107">Middleware class</span></span>

<span data-ttu-id="5e617-108">Middleware genellikle bir sınıfta kapsüllenir ve bir uzatma yöntemi ile maruz kalmaktadır.</span><span class="sxs-lookup"><span data-stu-id="5e617-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="5e617-109">Bir sorgu dizesinden geçerli istek için kültür ayarlar aşağıdaki ara yazılım, düşünün:</span><span class="sxs-lookup"><span data-stu-id="5e617-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="5e617-110">Önceki örnek kod, bir ara yazılım bileşeni oluşturmayı göstermek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="5e617-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="5e617-111">ASP.NET Core'un yerleşik yerelleştirme desteği <xref:fundamentals/localization>için bkz.</span><span class="sxs-lookup"><span data-stu-id="5e617-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="5e617-112">Kültür geçerek ara test edin.</span><span class="sxs-lookup"><span data-stu-id="5e617-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="5e617-113">Örneğin, istek `https://localhost:5001/?culture=no`.</span><span class="sxs-lookup"><span data-stu-id="5e617-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="5e617-114">Aşağıdaki kod ara yazılım temsilcisini bir sınıfa taşır:</span><span class="sxs-lookup"><span data-stu-id="5e617-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="5e617-115">Ara yazılım sınıfı şunları içermelidir:</span><span class="sxs-lookup"><span data-stu-id="5e617-115">The middleware class must include:</span></span>

* <span data-ttu-id="5e617-116">Bir tür <xref:Microsoft.AspNetCore.Http.RequestDelegate>parametresi olan bir ortak yapıcı.</span><span class="sxs-lookup"><span data-stu-id="5e617-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="5e617-117">Adlandırılmış `Invoke` genel `InvokeAsync`bir yöntem veya .</span><span class="sxs-lookup"><span data-stu-id="5e617-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="5e617-118">Bu yöntem şunları yapmalı:</span><span class="sxs-lookup"><span data-stu-id="5e617-118">This method must:</span></span>
  * <span data-ttu-id="5e617-119">Bir `Task`.</span><span class="sxs-lookup"><span data-stu-id="5e617-119">Return a `Task`.</span></span>
  * <span data-ttu-id="5e617-120">Türünün <xref:Microsoft.AspNetCore.Http.HttpContext>ilk parametresini kabul edin.</span><span class="sxs-lookup"><span data-stu-id="5e617-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="5e617-121">Yapıcı için ek parametreler `Invoke` / `InvokeAsync` ve [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)tarafından doldurulur.</span><span class="sxs-lookup"><span data-stu-id="5e617-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="5e617-122">Ara yazılım bağımlılıkları</span><span class="sxs-lookup"><span data-stu-id="5e617-122">Middleware dependencies</span></span>

<span data-ttu-id="5e617-123">Ara yazılım, yapıcısındaki bağımlılıklarını ortaya çıkararak [Açık Bağımlılıklar İlkesi'ni](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) izlemelidir.</span><span class="sxs-lookup"><span data-stu-id="5e617-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="5e617-124">Middleware *uygulama ömrü*başına bir kez inşa edilmiştir.</span><span class="sxs-lookup"><span data-stu-id="5e617-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="5e617-125">Bir istek içinde ara yazılımlarla hizmetleri paylaşmanız [gerekiyorsa, isteğ başına](#per-request-middleware-dependencies) ara yazılım bağımlılıkları bölümüne bakın.</span><span class="sxs-lookup"><span data-stu-id="5e617-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="5e617-126">Ara yazılım bileşenleri bağımlılıklarını [bağımlılık enjeksiyonundan (DI)](xref:fundamentals/dependency-injection) oluşturucu parametreleri aracılığıyla çözebilir.</span><span class="sxs-lookup"><span data-stu-id="5e617-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="5e617-127">[UseMiddleware&lt;&gt; T](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) ayrıca ek parametreleri doğrudan kabul edebilir.</span><span class="sxs-lookup"><span data-stu-id="5e617-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="5e617-128">İstek başına ara yazılım bağımlılıkları</span><span class="sxs-lookup"><span data-stu-id="5e617-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="5e617-129">Ara yazılım, istek başına değil, uygulama başlangıç noktasında oluşturulduğundan, ara yazılım üreticileri tarafından kullanılan *kapsamlı* yaşam süresi hizmetleri her istek sırasında diğer bağımlılık enjekte edilen türler ile paylaşılmaz.</span><span class="sxs-lookup"><span data-stu-id="5e617-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="5e617-130">Ara yazılımınız ve diğer türler arasında *kapsamlı* bir hizmet paylaşmanız `Invoke` gerekiyorsa, bu hizmetleri yöntemin imzasına ekleyin.</span><span class="sxs-lookup"><span data-stu-id="5e617-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="5e617-131">Yöntem, `Invoke` DI tarafından doldurulan ek parametreleri kabul edebilir:</span><span class="sxs-lookup"><span data-stu-id="5e617-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="middleware-extension-method"></a><span data-ttu-id="5e617-132">Ara yazılım uzatma yöntemi</span><span class="sxs-lookup"><span data-stu-id="5e617-132">Middleware extension method</span></span>

<span data-ttu-id="5e617-133">Aşağıdaki uzatma yöntemi aracılığı ile <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>middleware ortaya çıkarır:</span><span class="sxs-lookup"><span data-stu-id="5e617-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="5e617-134">Aşağıdaki kod middleware `Startup.Configure`çağırır:</span><span class="sxs-lookup"><span data-stu-id="5e617-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="5e617-135">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="5e617-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
