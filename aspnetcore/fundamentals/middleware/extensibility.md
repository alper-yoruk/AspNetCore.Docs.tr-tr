---
title: ASP.NET Core'da fabrika tabanlı ara yazılım etkinleştirme
author: rick-anderson
description: ASP.NET Core'da fabrika tabanlı etkinleştirme uygulamasıyla güçlü bir şekilde dakti-si yazılan ara yazılımları nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: abc6268584d12fe43d972c79a99316b94e8bee4b
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663095"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="9169a-103">ASP.NET Core'da fabrika tabanlı ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="9169a-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9169a-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>[ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="9169a-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="9169a-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>uzatma yöntemleri, bir ara yazılımın kayıtlı <xref:Microsoft.AspNetCore.Http.IMiddleware>türü uygulanıp uygulanmayanolmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="9169a-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="9169a-106">Varsa, kapsayıcıda <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kayıtlı örnek, kuralı tabanlı <xref:Microsoft.AspNetCore.Http.IMiddleware> ara yazılım etkinleştirme mantığını kullanmak yerine uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9169a-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="9169a-107">Ara yazılım, uygulamanın servis kapsayıcısında [kapsamlı veya geçici](xref:fundamentals/dependency-injection#service-lifetimes) bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9169a-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="9169a-108">Avantajlar:</span><span class="sxs-lookup"><span data-stu-id="9169a-108">Benefits:</span></span>

* <span data-ttu-id="9169a-109">İstemci isteği başına etkinleştirme (kapsamlı hizmetlerin enjeksiyonu)</span><span class="sxs-lookup"><span data-stu-id="9169a-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="9169a-110">Middleware güçlü yazma</span><span class="sxs-lookup"><span data-stu-id="9169a-110">Strong typing of middleware</span></span>

<span data-ttu-id="9169a-111"><xref:Microsoft.AspNetCore.Http.IMiddleware>istemci isteği (bağlantı) başına etkinleştirilir, böylece kapsamlı hizmetler ara yazılımın oluşturucusuna enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="9169a-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="9169a-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9169a-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="9169a-113">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="9169a-113">IMiddleware</span></span>

<span data-ttu-id="9169a-114"><xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9169a-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="9169a-115">[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler <xref:System.Threading.Tasks.Task> ve ara yazılımın yürütülmesini temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9169a-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="9169a-116">Ara yazılım lar sözleşme ile etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="9169a-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="9169a-117">Ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>tarafından etkinleştirildi:</span><span class="sxs-lookup"><span data-stu-id="9169a-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="9169a-118">Uzantılar ara yazılımlar için oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9169a-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="9169a-119">Nesneleri fabrikada etkinleştirilen ara yazılıma geçirmek mümkün <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>değildir:</span><span class="sxs-lookup"><span data-stu-id="9169a-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="9169a-120">Fabrikada etkinleştirilen ara yazılım, dahili `Startup.ConfigureServices`konteynere aşağıdaki şekilde eklenir:</span><span class="sxs-lookup"><span data-stu-id="9169a-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="9169a-121">Her iki ara yazılım da istek `Startup.Configure`işleme boru hattında kayıtlıdır:</span><span class="sxs-lookup"><span data-stu-id="9169a-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="9169a-122">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="9169a-122">IMiddlewareFactory</span></span>

<span data-ttu-id="9169a-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="9169a-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="9169a-124">Ara yazılım fabrikası uygulaması kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9169a-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="9169a-125">Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="9169a-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9169a-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>[ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="9169a-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="9169a-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>uzatma yöntemleri, bir ara yazılımın kayıtlı <xref:Microsoft.AspNetCore.Http.IMiddleware>türü uygulanıp uygulanmayanolmadığını denetler.</span><span class="sxs-lookup"><span data-stu-id="9169a-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="9169a-128">Varsa, kapsayıcıda <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kayıtlı örnek, kuralı tabanlı <xref:Microsoft.AspNetCore.Http.IMiddleware> ara yazılım etkinleştirme mantığını kullanmak yerine uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="9169a-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="9169a-129">Ara yazılım, uygulamanın servis kapsayıcısında [kapsamlı veya geçici](xref:fundamentals/dependency-injection#service-lifetimes) bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9169a-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="9169a-130">Avantajlar:</span><span class="sxs-lookup"><span data-stu-id="9169a-130">Benefits:</span></span>

* <span data-ttu-id="9169a-131">İstemci isteği başına etkinleştirme (kapsamlı hizmetlerin enjeksiyonu)</span><span class="sxs-lookup"><span data-stu-id="9169a-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="9169a-132">Middleware güçlü yazma</span><span class="sxs-lookup"><span data-stu-id="9169a-132">Strong typing of middleware</span></span>

<span data-ttu-id="9169a-133"><xref:Microsoft.AspNetCore.Http.IMiddleware>istemci isteği (bağlantı) başına etkinleştirilir, böylece kapsamlı hizmetler ara yazılımın oluşturucusuna enjekte edilebilir.</span><span class="sxs-lookup"><span data-stu-id="9169a-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="9169a-134">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="9169a-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="9169a-135">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="9169a-135">IMiddleware</span></span>

<span data-ttu-id="9169a-136"><xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar.</span><span class="sxs-lookup"><span data-stu-id="9169a-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="9169a-137">[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler <xref:System.Threading.Tasks.Task> ve ara yazılımın yürütülmesini temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="9169a-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="9169a-138">Ara yazılım lar sözleşme ile etkinleştirilir:</span><span class="sxs-lookup"><span data-stu-id="9169a-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="9169a-139">Ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>tarafından etkinleştirildi:</span><span class="sxs-lookup"><span data-stu-id="9169a-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="9169a-140">Uzantılar ara yazılımlar için oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="9169a-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="9169a-141">Nesneleri fabrikada etkinleştirilen ara yazılıma geçirmek mümkün <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>değildir:</span><span class="sxs-lookup"><span data-stu-id="9169a-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="9169a-142">Fabrikada etkinleştirilen ara yazılım, dahili `Startup.ConfigureServices`konteynere aşağıdaki şekilde eklenir:</span><span class="sxs-lookup"><span data-stu-id="9169a-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="9169a-143">Her iki ara yazılım da istek `Startup.Configure`işleme boru hattında kayıtlıdır:</span><span class="sxs-lookup"><span data-stu-id="9169a-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="9169a-144">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="9169a-144">IMiddlewareFactory</span></span>

<span data-ttu-id="9169a-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="9169a-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="9169a-146">Ara yazılım fabrikası uygulaması kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="9169a-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="9169a-147">Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="9169a-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9169a-148">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="9169a-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
