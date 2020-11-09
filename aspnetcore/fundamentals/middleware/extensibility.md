---
title: ASP.NET Core 'de fabrika tabanlı ara yazılım etkinleştirmesi
author: rick-anderson
description: ASP.NET Core ' de fabrika tabanlı etkinleştirme uygulamasıyla, türü kesin belirlenmiş bir ara yazılımı nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: fundamentals/middleware/extensibility
ms.openlocfilehash: 125ebbba9fadf0db357fe7c69af7ca0d8ddc8041
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057498"
---
# <a name="factory-based-middleware-activation-in-aspnet-core"></a><span data-ttu-id="f1c12-103">ASP.NET Core 'de fabrika tabanlı ara yazılım etkinleştirmesi</span><span class="sxs-lookup"><span data-stu-id="f1c12-103">Factory-based middleware activation in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f1c12-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> , [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="f1c12-104"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="f1c12-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> Uzantı yöntemleri bir ara yazılımın kayıtlı türünün uygulayıp uygulamadığını denetler <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="f1c12-105"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="f1c12-106">Bu durumda, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kapsayıcıya kaydedilen örnek, <xref:Microsoft.AspNetCore.Http.IMiddleware> kural tabanlı ara yazılım etkinleştirme mantığını kullanmak yerine, uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1c12-106">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="f1c12-107">Ara yazılım, uygulamanın hizmet kapsayıcısında [kapsamlı veya geçici bir hizmet](xref:fundamentals/dependency-injection#service-lifetimes) olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1c12-107">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="f1c12-108">Avantajlar:</span><span class="sxs-lookup"><span data-stu-id="f1c12-108">Benefits:</span></span>

* <span data-ttu-id="f1c12-109">İstemci isteği başına etkinleştirme (kapsamlı hizmetler ekleme)</span><span class="sxs-lookup"><span data-stu-id="f1c12-109">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="f1c12-110">Ara yazılım sıkı yazma</span><span class="sxs-lookup"><span data-stu-id="f1c12-110">Strong typing of middleware</span></span>

<span data-ttu-id="f1c12-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> istemci isteği başına etkinleştirilir (bağlantı), bu nedenle kapsamlı hizmetler ara yazılım oluşturucusuna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f1c12-111"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="f1c12-112">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1c12-112">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="f1c12-113">Imıddliware</span><span class="sxs-lookup"><span data-stu-id="f1c12-113">IMiddleware</span></span>

<span data-ttu-id="f1c12-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f1c12-114"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="f1c12-115">[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler ve <xref:System.Threading.Tasks.Task> Ara yazılım yürütmesini temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="f1c12-115">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="f1c12-116">Kurala göre etkinleştirilen ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="f1c12-116">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="f1c12-117">Etkinleştirilen ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="f1c12-117">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="f1c12-118">Middlewares için Uzantılar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f1c12-118">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="f1c12-119">Nesneleri fabrikada etkinleştirilen bir ara yazılıma geçirmek mümkün değildir <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="f1c12-119">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="f1c12-120">Fabrikada etkinleştirilen ara yazılım, içindeki yerleşik kapsayıcıya eklenir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f1c12-120">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f1c12-121">Her iki middlewares de istek işleme ardışık düzeninde kaydedilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f1c12-121">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="f1c12-122">Imıddliwarefactory</span><span class="sxs-lookup"><span data-stu-id="f1c12-122">IMiddlewareFactory</span></span>

<span data-ttu-id="f1c12-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1c12-123"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="f1c12-124">Ara yazılım Fabrikası Uygulama, kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1c12-124">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="f1c12-125">Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> [Microsoft. Aspnetcore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f1c12-125">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f1c12-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> , [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.</span><span class="sxs-lookup"><span data-stu-id="f1c12-126"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware> is an extensibility point for [middleware](xref:fundamentals/middleware/index) activation.</span></span>

<span data-ttu-id="f1c12-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> Uzantı yöntemleri bir ara yazılımın kayıtlı türünün uygulayıp uygulamadığını denetler <xref:Microsoft.AspNetCore.Http.IMiddleware> .</span><span class="sxs-lookup"><span data-stu-id="f1c12-127"><xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> extension methods check if a middleware's registered type implements <xref:Microsoft.AspNetCore.Http.IMiddleware>.</span></span> <span data-ttu-id="f1c12-128">Bu durumda, <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kapsayıcıya kaydedilen örnek, <xref:Microsoft.AspNetCore.Http.IMiddleware> kural tabanlı ara yazılım etkinleştirme mantığını kullanmak yerine, uygulamayı çözmek için kullanılır.</span><span class="sxs-lookup"><span data-stu-id="f1c12-128">If it does, the <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> instance registered in the container is used to resolve the <xref:Microsoft.AspNetCore.Http.IMiddleware> implementation instead of using the convention-based middleware activation logic.</span></span> <span data-ttu-id="f1c12-129">Ara yazılım, uygulamanın hizmet kapsayıcısında [kapsamlı veya geçici bir hizmet](xref:fundamentals/dependency-injection#service-lifetimes) olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1c12-129">The middleware is registered as a [scoped or transient service](xref:fundamentals/dependency-injection#service-lifetimes) in the app's service container.</span></span>

<span data-ttu-id="f1c12-130">Avantajlar:</span><span class="sxs-lookup"><span data-stu-id="f1c12-130">Benefits:</span></span>

* <span data-ttu-id="f1c12-131">İstemci isteği başına etkinleştirme (kapsamlı hizmetler ekleme)</span><span class="sxs-lookup"><span data-stu-id="f1c12-131">Activation per client request (injection of scoped services)</span></span>
* <span data-ttu-id="f1c12-132">Ara yazılım sıkı yazma</span><span class="sxs-lookup"><span data-stu-id="f1c12-132">Strong typing of middleware</span></span>

<span data-ttu-id="f1c12-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> istemci isteği başına etkinleştirilir (bağlantı), bu nedenle kapsamlı hizmetler ara yazılım oluşturucusuna eklenebilir.</span><span class="sxs-lookup"><span data-stu-id="f1c12-133"><xref:Microsoft.AspNetCore.Http.IMiddleware> is activated per client request (connection), so scoped services can be injected into the middleware's constructor.</span></span>

<span data-ttu-id="f1c12-134">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="f1c12-134">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="imiddleware"></a><span data-ttu-id="f1c12-135">Imıddliware</span><span class="sxs-lookup"><span data-stu-id="f1c12-135">IMiddleware</span></span>

<span data-ttu-id="f1c12-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="f1c12-136"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span> <span data-ttu-id="f1c12-137">[InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler ve <xref:System.Threading.Tasks.Task> Ara yazılım yürütmesini temsil eden bir döndürür.</span><span class="sxs-lookup"><span data-stu-id="f1c12-137">The [InvokeAsync(HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) method handles requests and returns a <xref:System.Threading.Tasks.Task> that represents the execution of the middleware.</span></span>

<span data-ttu-id="f1c12-138">Kurala göre etkinleştirilen ara yazılım:</span><span class="sxs-lookup"><span data-stu-id="f1c12-138">Middleware activated by convention:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

<span data-ttu-id="f1c12-139">Etkinleştirilen ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> :</span><span class="sxs-lookup"><span data-stu-id="f1c12-139">Middleware activated by <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="f1c12-140">Middlewares için Uzantılar oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="f1c12-140">Extensions are created for the middlewares:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="f1c12-141">Nesneleri fabrikada etkinleştirilen bir ara yazılıma geçirmek mümkün değildir <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*> :</span><span class="sxs-lookup"><span data-stu-id="f1c12-141">It isn't possible to pass objects to the factory-activated middleware with <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>:</span></span>

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

<span data-ttu-id="f1c12-142">Fabrikada etkinleştirilen ara yazılım, içindeki yerleşik kapsayıcıya eklenir `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="f1c12-142">The factory-activated middleware is added to the built-in container in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

<span data-ttu-id="f1c12-143">Her iki middlewares de istek işleme ardışık düzeninde kaydedilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="f1c12-143">Both middlewares are registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a><span data-ttu-id="f1c12-144">Imıddliwarefactory</span><span class="sxs-lookup"><span data-stu-id="f1c12-144">IMiddlewareFactory</span></span>

<span data-ttu-id="f1c12-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="f1c12-145"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span> <span data-ttu-id="f1c12-146">Ara yazılım Fabrikası Uygulama, kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.</span><span class="sxs-lookup"><span data-stu-id="f1c12-146">The middleware factory implementation is registered in the container as a scoped service.</span></span>

<span data-ttu-id="f1c12-147">Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> [Microsoft. Aspnetcore. http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.</span><span class="sxs-lookup"><span data-stu-id="f1c12-147">The default <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> implementation, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>, is found in the [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) package.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f1c12-148">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="f1c12-148">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
