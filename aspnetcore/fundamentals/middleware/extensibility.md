---
title: ASP.NET Core 'de fabrika tabanlı ara yazılım etkinleştirmesi
author: rick-anderson
description: ASP.NET Core ' de fabrika tabanlı etkinleştirme uygulamasıyla, türü kesin belirlenmiş bir ara yazılımı nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 108d7d343a08bff8b5665df7b149f7f952220459
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774463"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="cf449-103">ASP.NET Core 'de fabrika tabanlı ara yazılım etkinleştirmesi</span><span class="sxs-lookup"><span data-stu-id="cf449-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cf449-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>, [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="cf449-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="cf449-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>Uzantı yöntemleri bir ara yazılımın kayıtlı türünün uygulayıp uygulamadığını <xref:Microsoft.AspNetCore.Http.IMiddleware>denetler.</span><span class="sxs-lookup"><span data-stu-id="cf449-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="cf449-106">Bu durumda, kapsayıcıya kaydedilen <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> örnek, kural tabanlı ara yazılım etkinleştirme mantığını kullanmak yerine <xref:Microsoft.AspNetCore.Http.IMiddleware> , uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cf449-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="cf449-107">Ara yazılım, uygulamanın hizmet kapsayıcısında [kapsamlı veya geçici bir hizmet](xref:fundamentals/dependency-injection#service-lifetimes) olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cf449-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="cf449-108">Avantajlar:</span><span class="sxs-lookup"><span data-stu-id="cf449-108">Benefits:</span></span>

* <span data-ttu-id="cf449-109">İstemci isteği başına etkinleştirme (kapsamlı hizmetler ekleme)</span><span class="sxs-lookup"><span data-stu-id="cf449-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="cf449-110">Ara yazılım sıkı yazma</span><span class="sxs-lookup"><span data-stu-id="cf449-110">Strong typing of middleware</span></span>

<span data-ttu-id="cf449-111"><xref:Microsoft.AspNetCore.Http.IMiddleware>istemci isteği başına etkinleştirilir (bağlantı), bu nedenle kapsamlı hizmetler ara yazılım oluşturucusuna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="cf449-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="cf449-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf449-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="cf449-113">Imıddliware</span><span class="sxs-lookup"><span data-stu-id="cf449-113">IMiddleware</span></span>

<span data-ttu-id="cf449-114"><xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cf449-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="cf449-115">[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler ve ara yazılım yürütmesini <xref:System.Threading.Tasks.Task> temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="cf449-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="cf449-116">Kurala göre etkinleştirilen ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="cf449-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="cf449-117">Etkinleştirilen ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="cf449-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="cf449-118">Middlewares için Uzantılar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="cf449-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="cf449-119">Nesneleri fabrikada etkinleştirilen bir ara yazılıma geçirmek mümkün değildir <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="cf449-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="cf449-120">Fabrikada etkinleştirilen ara yazılım, içindeki `Startup.ConfigureServices`yerleşik kapsayıcıya eklenir:</span><span class="sxs-lookup"><span data-stu-id="cf449-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="cf449-121">Her iki middlewares de `Startup.Configure`istek işleme ardışık düzeninde kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="cf449-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="cf449-122">Imıddliwarefactory</span><span class="sxs-lookup"><span data-stu-id="cf449-122">IMiddlewareFactory</span></span>

<span data-ttu-id="cf449-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf449-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="cf449-124">Ara yazılım Fabrikası Uygulama, kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cf449-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="cf449-125">Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, [Microsoft. aspnetcore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="cf449-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cf449-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>, [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="cf449-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="cf449-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>Uzantı yöntemleri bir ara yazılımın kayıtlı türünün uygulayıp uygulamadığını <xref:Microsoft.AspNetCore.Http.IMiddleware>denetler.</span><span class="sxs-lookup"><span data-stu-id="cf449-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="cf449-128">Bu durumda, kapsayıcıya kaydedilen <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> örnek, kural tabanlı ara yazılım etkinleştirme mantığını kullanmak yerine <xref:Microsoft.AspNetCore.Http.IMiddleware> , uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="cf449-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="cf449-129">Ara yazılım, uygulamanın hizmet kapsayıcısında [kapsamlı veya geçici bir hizmet](xref:fundamentals/dependency-injection#service-lifetimes) olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cf449-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="cf449-130">Avantajlar:</span><span class="sxs-lookup"><span data-stu-id="cf449-130">Benefits:</span></span>

* <span data-ttu-id="cf449-131">İstemci isteği başına etkinleştirme (kapsamlı hizmetler ekleme)</span><span class="sxs-lookup"><span data-stu-id="cf449-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="cf449-132">Ara yazılım sıkı yazma</span><span class="sxs-lookup"><span data-stu-id="cf449-132">Strong typing of middleware</span></span>

<span data-ttu-id="cf449-133"><xref:Microsoft.AspNetCore.Http.IMiddleware>istemci isteği başına etkinleştirilir (bağlantı), bu nedenle kapsamlı hizmetler ara yazılım oluşturucusuna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="cf449-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="cf449-134">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="cf449-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="cf449-135">Imıddliware</span><span class="sxs-lookup"><span data-stu-id="cf449-135">IMiddleware</span></span>

<span data-ttu-id="cf449-136"><xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="cf449-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="cf449-137">[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler ve ara yazılım yürütmesini <xref:System.Threading.Tasks.Task> temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="cf449-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="cf449-138">Kurala göre etkinleştirilen ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="cf449-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="cf449-139">Etkinleştirilen ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span><span class="sxs-lookup"><span data-stu-id="cf449-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="cf449-140">Middlewares için Uzantılar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="cf449-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="cf449-141">Nesneleri fabrikada etkinleştirilen bir ara yazılıma geçirmek mümkün değildir <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span><span class="sxs-lookup"><span data-stu-id="cf449-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="cf449-142">Fabrikada etkinleştirilen ara yazılım, içindeki `Startup.ConfigureServices`yerleşik kapsayıcıya eklenir:</span><span class="sxs-lookup"><span data-stu-id="cf449-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="cf449-143">Her iki middlewares de `Startup.Configure`istek işleme ardışık düzeninde kaydedilir:</span><span class="sxs-lookup"><span data-stu-id="cf449-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="cf449-144">Imıddliwarefactory</span><span class="sxs-lookup"><span data-stu-id="cf449-144">IMiddlewareFactory</span></span>

<span data-ttu-id="cf449-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="cf449-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="cf449-146">Ara yazılım Fabrikası Uygulama, kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="cf449-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="cf449-147">Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, [Microsoft. aspnetcore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="cf449-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cf449-148">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="cf449-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
