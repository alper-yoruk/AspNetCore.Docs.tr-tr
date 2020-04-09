---
title: ASP.NET Core'da üçüncü taraf bir kapsayıcı ile ara yazılım etkinleştirme
author: rick-anderson
description: ASP.NET Core'da fabrika tabanlı etkinleştirme ve üçüncü taraf bir kapsayıcı ile güçlü bir şekilde yazılan ara yazılımları nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: a5c5bf6dff6ef795add075df932dd625129ef793
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2020
ms.locfileid: "78663137"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="c4dfd-103">ASP.NET Core'da üçüncü taraf bir kapsayıcı ile ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="c4dfd-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c4dfd-104">Bu makalede, nasıl <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kullanılacağını ve <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf bir kapsayıcı ile [ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası olarak gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="c4dfd-105">Giriş hakkında bilgi `IMiddlewareFactory` `IMiddleware`için <xref:fundamentals/middleware/extensibility>ve , bkz.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="c4dfd-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c4dfd-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c4dfd-107">Örnek uygulama, bir `IMiddlewareFactory` uygulama tarafından ara `SimpleInjectorMiddlewareFactory`yazılım aktivasyonu gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="c4dfd-108">Örnek basit [enjektör](https://simpleinjector.org) bağımlılığı enjeksiyon (DI) konteyner kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="c4dfd-109">Örneğin ara yazılım uygulaması, sorgu dize parametresi`key`( tarafından sağlanan değeri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="c4dfd-110">Ara yazılım, sorgu dize değerini bellek içi bir veritabanına kaydetmek için enjekte edilmiş bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="c4dfd-111">Örnek uygulama sadece gösteri amacıyla [Basit Enjektör](https://github.com/simpleinjector/SimpleInjector) kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="c4dfd-112">Basit Enjektör kullanımı bir ciro değildir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="c4dfd-113">Basit Enjektör belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, Basit Enjektör'ün koruyucuları tarafından önerilir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="c4dfd-114">Daha fazla bilgi için, [Basit Enjektör belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [Basit Enjektör GitHub deposuna](https://github.com/simpleinjector/SimpleInjector)bakın.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="c4dfd-115">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="c4dfd-115">IMiddlewareFactory</span></span>

<span data-ttu-id="c4dfd-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="c4dfd-117">Örnek uygulamada, bir `SimpleInjectorActivatedMiddleware` örnek oluşturmak için bir ara yazılım fabrikası uygulanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="c4dfd-118">Ara yazılım fabrikası, ara yazılımı çözmek için Basit Enjektör kabını kullanır:</span><span class="sxs-lookup"><span data-stu-id="c4dfd-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="c4dfd-119">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="c4dfd-119">IMiddleware</span></span>

<span data-ttu-id="c4dfd-120"><xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="c4dfd-121">Middleware bir `IMiddlewareFactory` uygulama tarafından aktive (*Middleware / SimpleInjectorActivatedMiddleware.cs):*</span><span class="sxs-lookup"><span data-stu-id="c4dfd-121">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="c4dfd-122">Middleware *(Middleware/MiddlewareExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="c4dfd-122">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="c4dfd-123">`Startup.ConfigureServices`birkaç görevi gerçekleştirmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="c4dfd-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="c4dfd-124">Basit Enjektör konteynerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="c4dfd-125">Fabrikayı ve ara yazılımı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="c4dfd-126">Uygulamanın veritabanı bağlamını Basit Enjektör kapsayıcısından kullanılabilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="c4dfd-127">Ara yazılım istek işleme boru hattında `Startup.Configure`kayıtlıdır:</span><span class="sxs-lookup"><span data-stu-id="c4dfd-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="c4dfd-128">Bu makalede, nasıl <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kullanılacağını ve <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf bir kapsayıcı ile [ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası olarak gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="c4dfd-129">Giriş hakkında bilgi `IMiddlewareFactory` `IMiddleware`için <xref:fundamentals/middleware/extensibility>ve , bkz.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="c4dfd-130">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="c4dfd-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c4dfd-131">Örnek uygulama, bir `IMiddlewareFactory` uygulama tarafından ara `SimpleInjectorMiddlewareFactory`yazılım aktivasyonu gösterir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="c4dfd-132">Örnek basit [enjektör](https://simpleinjector.org) bağımlılığı enjeksiyon (DI) konteyner kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="c4dfd-133">Örneğin ara yazılım uygulaması, sorgu dize parametresi`key`( tarafından sağlanan değeri kaydeder.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="c4dfd-134">Ara yazılım, sorgu dize değerini bellek içi bir veritabanına kaydetmek için enjekte edilmiş bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="c4dfd-135">Örnek uygulama sadece gösteri amacıyla [Basit Enjektör](https://github.com/simpleinjector/SimpleInjector) kullanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="c4dfd-136">Basit Enjektör kullanımı bir ciro değildir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="c4dfd-137">Basit Enjektör belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, Basit Enjektör'ün koruyucuları tarafından önerilir.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="c4dfd-138">Daha fazla bilgi için, [Basit Enjektör belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [Basit Enjektör GitHub deposuna](https://github.com/simpleinjector/SimpleInjector)bakın.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="c4dfd-139">IMiddlewareFactory</span><span class="sxs-lookup"><span data-stu-id="c4dfd-139">IMiddlewareFactory</span></span>

<span data-ttu-id="c4dfd-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="c4dfd-141">Örnek uygulamada, bir `SimpleInjectorActivatedMiddleware` örnek oluşturmak için bir ara yazılım fabrikası uygulanır.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="c4dfd-142">Ara yazılım fabrikası, ara yazılımı çözmek için Basit Enjektör kabını kullanır:</span><span class="sxs-lookup"><span data-stu-id="c4dfd-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="c4dfd-143">IMiddleware</span><span class="sxs-lookup"><span data-stu-id="c4dfd-143">IMiddleware</span></span>

<span data-ttu-id="c4dfd-144"><xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="c4dfd-145">Middleware bir `IMiddlewareFactory` uygulama tarafından aktive (*Middleware / SimpleInjectorActivatedMiddleware.cs):*</span><span class="sxs-lookup"><span data-stu-id="c4dfd-145">Middleware activated by an `IMiddlewareFactory` implementation (*Middleware/SimpleInjectorActivatedMiddleware.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="c4dfd-146">Middleware *(Middleware/MiddlewareExtensions.cs):*</span><span class="sxs-lookup"><span data-stu-id="c4dfd-146">An extension is created for the middleware (*Middleware/MiddlewareExtensions.cs*):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="c4dfd-147">`Startup.ConfigureServices`birkaç görevi gerçekleştirmesi gerekir:</span><span class="sxs-lookup"><span data-stu-id="c4dfd-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="c4dfd-148">Basit Enjektör konteynerini ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="c4dfd-149">Fabrikayı ve ara yazılımı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="c4dfd-150">Uygulamanın veritabanı bağlamını Basit Enjektör kapsayıcısından kullanılabilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="c4dfd-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="c4dfd-151">Ara yazılım istek işleme boru hattında `Startup.Configure`kayıtlıdır:</span><span class="sxs-lookup"><span data-stu-id="c4dfd-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="c4dfd-152">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="c4dfd-152">Additional resources</span></span>

* [<span data-ttu-id="c4dfd-153">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="c4dfd-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c4dfd-154">Fabrika tabanlı ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="c4dfd-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="c4dfd-155">Basit Enjektör GitHub deposu</span><span class="sxs-lookup"><span data-stu-id="c4dfd-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="c4dfd-156">Basit Enjektör belgeleri</span><span class="sxs-lookup"><span data-stu-id="c4dfd-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
