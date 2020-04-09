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
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>ASP.NET Core'da üçüncü taraf bir kapsayıcı ile ara yazılım etkinleştirme

::: moniker range=">= aspnetcore-3.0"

Bu makalede, nasıl <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kullanılacağını ve <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf bir kapsayıcı ile [ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası olarak gösterir. Giriş hakkında bilgi `IMiddlewareFactory` `IMiddleware`için <xref:fundamentals/middleware/extensibility>ve , bkz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama, bir `IMiddlewareFactory` uygulama tarafından ara `SimpleInjectorMiddlewareFactory`yazılım aktivasyonu gösterir. Örnek basit [enjektör](https://simpleinjector.org) bağımlılığı enjeksiyon (DI) konteyner kullanır.

Örneğin ara yazılım uygulaması, sorgu dize parametresi`key`( tarafından sağlanan değeri kaydeder. Ara yazılım, sorgu dize değerini bellek içi bir veritabanına kaydetmek için enjekte edilmiş bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.

> [!NOTE]
> Örnek uygulama sadece gösteri amacıyla [Basit Enjektör](https://github.com/simpleinjector/SimpleInjector) kullanır. Basit Enjektör kullanımı bir ciro değildir. Basit Enjektör belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, Basit Enjektör'ün koruyucuları tarafından önerilir. Daha fazla bilgi için, [Basit Enjektör belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [Basit Enjektör GitHub deposuna](https://github.com/simpleinjector/SimpleInjector)bakın.

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.

Örnek uygulamada, bir `SimpleInjectorActivatedMiddleware` örnek oluşturmak için bir ara yazılım fabrikası uygulanır. Ara yazılım fabrikası, ara yazılımı çözmek için Basit Enjektör kabını kullanır:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar.

Middleware bir `IMiddlewareFactory` uygulama tarafından aktive (*Middleware / SimpleInjectorActivatedMiddleware.cs):*

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Middleware *(Middleware/MiddlewareExtensions.cs):*

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`birkaç görevi gerçekleştirmesi gerekir:

* Basit Enjektör konteynerini ayarlayın.
* Fabrikayı ve ara yazılımı kaydedin.
* Uygulamanın veritabanı bağlamını Basit Enjektör kapsayıcısından kullanılabilir hale getirin.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Ara yazılım istek işleme boru hattında `Startup.Configure`kayıtlıdır:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede, nasıl <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kullanılacağını ve <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf bir kapsayıcı ile [ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası olarak gösterir. Giriş hakkında bilgi `IMiddlewareFactory` `IMiddleware`için <xref:fundamentals/middleware/extensibility>ve , bkz.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

Örnek uygulama, bir `IMiddlewareFactory` uygulama tarafından ara `SimpleInjectorMiddlewareFactory`yazılım aktivasyonu gösterir. Örnek basit [enjektör](https://simpleinjector.org) bağımlılığı enjeksiyon (DI) konteyner kullanır.

Örneğin ara yazılım uygulaması, sorgu dize parametresi`key`( tarafından sağlanan değeri kaydeder. Ara yazılım, sorgu dize değerini bellek içi bir veritabanına kaydetmek için enjekte edilmiş bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.

> [!NOTE]
> Örnek uygulama sadece gösteri amacıyla [Basit Enjektör](https://github.com/simpleinjector/SimpleInjector) kullanır. Basit Enjektör kullanımı bir ciro değildir. Basit Enjektör belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, Basit Enjektör'ün koruyucuları tarafından önerilir. Daha fazla bilgi için, [Basit Enjektör belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [Basit Enjektör GitHub deposuna](https://github.com/simpleinjector/SimpleInjector)bakın.

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.

Örnek uygulamada, bir `SimpleInjectorActivatedMiddleware` örnek oluşturmak için bir ara yazılım fabrikası uygulanır. Ara yazılım fabrikası, ara yazılımı çözmek için Basit Enjektör kabını kullanır:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar.

Middleware bir `IMiddlewareFactory` uygulama tarafından aktive (*Middleware / SimpleInjectorActivatedMiddleware.cs):*

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Middleware *(Middleware/MiddlewareExtensions.cs):*

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`birkaç görevi gerçekleştirmesi gerekir:

* Basit Enjektör konteynerini ayarlayın.
* Fabrikayı ve ara yazılımı kaydedin.
* Uygulamanın veritabanı bağlamını Basit Enjektör kapsayıcısından kullanılabilir hale getirin.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Ara yazılım istek işleme boru hattında `Startup.Configure`kayıtlıdır:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Ara yazılım](xref:fundamentals/middleware/index)
* [Fabrika tabanlı ara yazılım etkinleştirme](xref:fundamentals/middleware/extensibility)
* [Basit Enjektör GitHub deposu](https://github.com/simpleinjector/SimpleInjector)
* [Basit Enjektör belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html)
