---
title: ASP.NET Core bir üçüncü taraf kapsayıcısı ile ara yazılım etkinleştirme
author: rick-anderson
description: Fabrika tabanlı etkinleştirme ve ASP.NET Core bir üçüncü taraf kapsayıcısı ile kesin olarak belirlenmiş bir ara yazılımı nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: d322b1b3dda161c9948359253c3f7fee64a1f9ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057771"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a><span data-ttu-id="daa4b-103">ASP.NET Core bir üçüncü taraf kapsayıcısı ile ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="daa4b-103">Middleware activation with a third-party container in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="daa4b-104">Bu makalede <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> , <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf kapsayıcı ile [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası ve nasıl kullanılacağı gösterilir.</span><span class="sxs-lookup"><span data-stu-id="daa4b-104">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="daa4b-105">Ve hakkında giriş bilgileri `IMiddlewareFactory` için `IMiddleware` bkz <xref:fundamentals/middleware/extensibility> ..</span><span class="sxs-lookup"><span data-stu-id="daa4b-105">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="daa4b-106">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daa4b-106">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="daa4b-107">Örnek uygulama, bir uygulama tarafından ara yazılım etkinleştirmesini gösterir `IMiddlewareFactory` `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="daa4b-107">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="daa4b-108">Örnek, [basit Injector](https://simpleinjector.org) bağımlılık ekleme (dı) kapsayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="daa4b-108">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="daa4b-109">Örneğin ara yazılım uygulamasının bir sorgu dizesi parametresi () tarafından belirtilen değeri kaydeder `key` .</span><span class="sxs-lookup"><span data-stu-id="daa4b-109">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="daa4b-110">Ara yazılım, sorgu dizesi değerini bellek içi bir veritabanına kaydetmek için eklenen bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.</span><span class="sxs-lookup"><span data-stu-id="daa4b-110">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="daa4b-111">Örnek uygulama, yalnızca tanıtım amacıyla [basit Injector](https://github.com/simpleinjector/SimpleInjector) kullanır.</span><span class="sxs-lookup"><span data-stu-id="daa4b-111">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="daa4b-112">Basit Injector kullanımı bir onay değildir.</span><span class="sxs-lookup"><span data-stu-id="daa4b-112">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="daa4b-113">Basit Injector belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, basit Injector 'ın sürdürülebilir tarafından önerilir.</span><span class="sxs-lookup"><span data-stu-id="daa4b-113">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="daa4b-114">Daha fazla bilgi için bkz. [basit Injector belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [basit injecgithub deposu](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="daa4b-114">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="daa4b-115">Imıddliwarefactory</span><span class="sxs-lookup"><span data-stu-id="daa4b-115">IMiddlewareFactory</span></span>

<span data-ttu-id="daa4b-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="daa4b-116"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="daa4b-117">Örnek uygulamada bir örnek oluşturmak için bir ara yazılım fabrikası uygulanır `SimpleInjectorActivatedMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="daa4b-117">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="daa4b-118">Ara yazılım fabrikası, ara yazılımı çözümlemek için basit Injector kapsayıcısını kullanır:</span><span class="sxs-lookup"><span data-stu-id="daa4b-118">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="daa4b-119">Imıddliware</span><span class="sxs-lookup"><span data-stu-id="daa4b-119">IMiddleware</span></span>

<span data-ttu-id="daa4b-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="daa4b-120"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="daa4b-121">Bir uygulama tarafından etkinleştirilen ara yazılım `IMiddlewareFactory` ( *Ara yazılım/Simpleınjectoractivatedara yazılım. cs* ):</span><span class="sxs-lookup"><span data-stu-id="daa4b-121">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="daa4b-122">Ara yazılım ( *Ara yazılım/MiddlewareExtensions. cs* ) için bir uzantı oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="daa4b-122">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="daa4b-123">`Startup.ConfigureServices` birkaç görev gerçekleştirmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="daa4b-123">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="daa4b-124">Basit Injector kapsayıcısını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="daa4b-124">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="daa4b-125">Fabrika ve ara yazılımı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="daa4b-125">Register the factory and middleware.</span></span>
* <span data-ttu-id="daa4b-126">Uygulamanın veritabanı bağlamını basit ınjtor kapsayıcısından kullanılabilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="daa4b-126">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="daa4b-127">Ara yazılım, içindeki istek işleme ardışık düzenine kaydedilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="daa4b-127">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="daa4b-128">Bu makalede <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> , <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf kapsayıcı ile [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası ve nasıl kullanılacağı gösterilir.</span><span class="sxs-lookup"><span data-stu-id="daa4b-128">This article demonstrates how to use <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> and <xref:Microsoft.AspNetCore.Http.IMiddleware> as an extensibility point for [middleware](xref:fundamentals/middleware/index) activation with a third-party container.</span></span> <span data-ttu-id="daa4b-129">Ve hakkında giriş bilgileri `IMiddlewareFactory` için `IMiddleware` bkz <xref:fundamentals/middleware/extensibility> ..</span><span class="sxs-lookup"><span data-stu-id="daa4b-129">For introductory information on `IMiddlewareFactory` and `IMiddleware`, see <xref:fundamentals/middleware/extensibility>.</span></span>

<span data-ttu-id="daa4b-130">[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="daa4b-130">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="daa4b-131">Örnek uygulama, bir uygulama tarafından ara yazılım etkinleştirmesini gösterir `IMiddlewareFactory` `SimpleInjectorMiddlewareFactory` .</span><span class="sxs-lookup"><span data-stu-id="daa4b-131">The sample app demonstrates middleware activation by an `IMiddlewareFactory` implementation, `SimpleInjectorMiddlewareFactory`.</span></span> <span data-ttu-id="daa4b-132">Örnek, [basit Injector](https://simpleinjector.org) bağımlılık ekleme (dı) kapsayıcısını kullanır.</span><span class="sxs-lookup"><span data-stu-id="daa4b-132">The sample uses the [Simple Injector](https://simpleinjector.org) dependency injection (DI) container.</span></span>

<span data-ttu-id="daa4b-133">Örneğin ara yazılım uygulamasının bir sorgu dizesi parametresi () tarafından belirtilen değeri kaydeder `key` .</span><span class="sxs-lookup"><span data-stu-id="daa4b-133">The sample's middleware implementation records the value provided by a query string parameter (`key`).</span></span> <span data-ttu-id="daa4b-134">Ara yazılım, sorgu dizesi değerini bellek içi bir veritabanına kaydetmek için eklenen bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.</span><span class="sxs-lookup"><span data-stu-id="daa4b-134">The middleware uses an injected database context (a scoped service) to record the query string value in an in-memory database.</span></span>

> [!NOTE]
> <span data-ttu-id="daa4b-135">Örnek uygulama, yalnızca tanıtım amacıyla [basit Injector](https://github.com/simpleinjector/SimpleInjector) kullanır.</span><span class="sxs-lookup"><span data-stu-id="daa4b-135">The sample app uses [Simple Injector](https://github.com/simpleinjector/SimpleInjector) purely for demonstration purposes.</span></span> <span data-ttu-id="daa4b-136">Basit Injector kullanımı bir onay değildir.</span><span class="sxs-lookup"><span data-stu-id="daa4b-136">Use of Simple Injector isn't an endorsement.</span></span> <span data-ttu-id="daa4b-137">Basit Injector belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, basit Injector 'ın sürdürülebilir tarafından önerilir.</span><span class="sxs-lookup"><span data-stu-id="daa4b-137">Middleware activation approaches described in the Simple Injector documentation and GitHub issues are recommended by the maintainers of Simple Injector.</span></span> <span data-ttu-id="daa4b-138">Daha fazla bilgi için bkz. [basit Injector belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [basit injecgithub deposu](https://github.com/simpleinjector/SimpleInjector).</span><span class="sxs-lookup"><span data-stu-id="daa4b-138">For more information, see the [Simple Injector documentation](https://simpleinjector.readthedocs.io/en/latest/index.html) and [Simple Injector GitHub repository](https://github.com/simpleinjector/SimpleInjector).</span></span>

## <a name="imiddlewarefactory"></a><span data-ttu-id="daa4b-139">Imıddliwarefactory</span><span class="sxs-lookup"><span data-stu-id="daa4b-139">IMiddlewareFactory</span></span>

<span data-ttu-id="daa4b-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> ara yazılım oluşturmak için yöntemler sağlar.</span><span class="sxs-lookup"><span data-stu-id="daa4b-140"><xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> provides methods to create middleware.</span></span>

<span data-ttu-id="daa4b-141">Örnek uygulamada bir örnek oluşturmak için bir ara yazılım fabrikası uygulanır `SimpleInjectorActivatedMiddleware` .</span><span class="sxs-lookup"><span data-stu-id="daa4b-141">In the sample app, a middleware factory is implemented to create an `SimpleInjectorActivatedMiddleware` instance.</span></span> <span data-ttu-id="daa4b-142">Ara yazılım fabrikası, ara yazılımı çözümlemek için basit Injector kapsayıcısını kullanır:</span><span class="sxs-lookup"><span data-stu-id="daa4b-142">The middleware factory uses the Simple Injector container to resolve the middleware:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a><span data-ttu-id="daa4b-143">Imıddliware</span><span class="sxs-lookup"><span data-stu-id="daa4b-143">IMiddleware</span></span>

<span data-ttu-id="daa4b-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.</span><span class="sxs-lookup"><span data-stu-id="daa4b-144"><xref:Microsoft.AspNetCore.Http.IMiddleware> defines middleware for the app's request pipeline.</span></span>

<span data-ttu-id="daa4b-145">Bir uygulama tarafından etkinleştirilen ara yazılım `IMiddlewareFactory` ( *Ara yazılım/Simpleınjectoractivatedara yazılım. cs* ):</span><span class="sxs-lookup"><span data-stu-id="daa4b-145">Middleware activated by an `IMiddlewareFactory` implementation ( *Middleware/SimpleInjectorActivatedMiddleware.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

<span data-ttu-id="daa4b-146">Ara yazılım ( *Ara yazılım/MiddlewareExtensions. cs* ) için bir uzantı oluşturulur:</span><span class="sxs-lookup"><span data-stu-id="daa4b-146">An extension is created for the middleware ( *Middleware/MiddlewareExtensions.cs* ):</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

<span data-ttu-id="daa4b-147">`Startup.ConfigureServices` birkaç görev gerçekleştirmeniz gerekir:</span><span class="sxs-lookup"><span data-stu-id="daa4b-147">`Startup.ConfigureServices` must perform several tasks:</span></span>

* <span data-ttu-id="daa4b-148">Basit Injector kapsayıcısını ayarlayın.</span><span class="sxs-lookup"><span data-stu-id="daa4b-148">Set up the Simple Injector container.</span></span>
* <span data-ttu-id="daa4b-149">Fabrika ve ara yazılımı kaydedin.</span><span class="sxs-lookup"><span data-stu-id="daa4b-149">Register the factory and middleware.</span></span>
* <span data-ttu-id="daa4b-150">Uygulamanın veritabanı bağlamını basit ınjtor kapsayıcısından kullanılabilir hale getirin.</span><span class="sxs-lookup"><span data-stu-id="daa4b-150">Make the app's database context available from the Simple Injector container.</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

<span data-ttu-id="daa4b-151">Ara yazılım, içindeki istek işleme ardışık düzenine kaydedilir `Startup.Configure` :</span><span class="sxs-lookup"><span data-stu-id="daa4b-151">The middleware is registered in the request processing pipeline in `Startup.Configure`:</span></span>

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="daa4b-152">Ek kaynaklar</span><span class="sxs-lookup"><span data-stu-id="daa4b-152">Additional resources</span></span>

* [<span data-ttu-id="daa4b-153">Ara yazılım</span><span class="sxs-lookup"><span data-stu-id="daa4b-153">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="daa4b-154">Fabrika tabanlı ara yazılım etkinleştirme</span><span class="sxs-lookup"><span data-stu-id="daa4b-154">Factory-based middleware activation</span></span>](xref:fundamentals/middleware/extensibility)
* [<span data-ttu-id="daa4b-155">Basit ınjecgithub deposu</span><span class="sxs-lookup"><span data-stu-id="daa4b-155">Simple Injector GitHub repository</span></span>](https://github.com/simpleinjector/SimpleInjector)
* [<span data-ttu-id="daa4b-156">Basit Injector belgeleri</span><span class="sxs-lookup"><span data-stu-id="daa4b-156">Simple Injector documentation</span></span>](https://simpleinjector.readthedocs.io/en/latest/index.html)
