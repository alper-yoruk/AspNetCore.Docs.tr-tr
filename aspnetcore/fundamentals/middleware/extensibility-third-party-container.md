---
title: ASP.NET Core bir üçüncü taraf kapsayıcısı ile ara yazılım etkinleştirme
author: rick-anderson
description: Fabrika tabanlı etkinleştirme ve ASP.NET Core bir üçüncü taraf kapsayıcısı ile kesin olarak belirlenmiş bir ara yazılımı nasıl kullanacağınızı öğrenin.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/22/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/extensibility-third-party-container
ms.openlocfilehash: 5d453de26b265b795768befeaa4071e7b0e1ec08
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017044"
---
# <a name="middleware-activation-with-a-third-party-container-in-aspnet-core"></a>ASP.NET Core bir üçüncü taraf kapsayıcısı ile ara yazılım etkinleştirme

::: moniker range=">= aspnetcore-3.0"

Bu makalede <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> , <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf kapsayıcı ile [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası ve nasıl kullanılacağı gösterilir. Ve hakkında giriş bilgileri `IMiddlewareFactory` için `IMiddleware` bkz <xref:fundamentals/middleware/extensibility> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama, bir uygulama tarafından ara yazılım etkinleştirmesini gösterir `IMiddlewareFactory` `SimpleInjectorMiddlewareFactory` . Örnek, [basit Injector](https://simpleinjector.org) bağımlılık ekleme (dı) kapsayıcısını kullanır.

Örneğin ara yazılım uygulamasının bir sorgu dizesi parametresi () tarafından belirtilen değeri kaydeder `key` . Ara yazılım, sorgu dizesi değerini bellek içi bir veritabanına kaydetmek için eklenen bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.

> [!NOTE]
> Örnek uygulama, yalnızca tanıtım amacıyla [basit Injector](https://github.com/simpleinjector/SimpleInjector) kullanır. Basit Injector kullanımı bir onay değildir. Basit Injector belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, basit Injector 'ın sürdürülebilir tarafından önerilir. Daha fazla bilgi için bkz. [basit Injector belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [basit injecgithub deposu](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>Imıddliwarefactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.

Örnek uygulamada bir örnek oluşturmak için bir ara yazılım fabrikası uygulanır `SimpleInjectorActivatedMiddleware` . Ara yazılım fabrikası, ara yazılımı çözümlemek için basit Injector kapsayıcısını kullanır:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>Imıddliware

<xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.

Bir uygulama tarafından etkinleştirilen ara yazılım `IMiddlewareFactory` (*Ara yazılım/Simpleınjectoractivatedara yazılım. cs*):

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Ara yazılım (*Ara yazılım/MiddlewareExtensions. cs*) için bir uzantı oluşturulur:

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`birkaç görev gerçekleştirmeniz gerekir:

* Basit Injector kapsayıcısını ayarlayın.
* Fabrika ve ara yazılımı kaydedin.
* Uygulamanın veritabanı bağlamını basit ınjtor kapsayıcısından kullanılabilir hale getirin.

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

Ara yazılım, içindeki istek işleme ardışık düzenine kaydedilir `Startup.Configure` :

[!code-csharp[](extensibility-third-party-container/samples/3.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Bu makalede <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> , <xref:Microsoft.AspNetCore.Http.IMiddleware> üçüncü taraf kapsayıcı ile [Ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktası ve nasıl kullanılacağı gösterilir. Ve hakkında giriş bilgileri `IMiddlewareFactory` için `IMiddleware` bkz <xref:fundamentals/middleware/extensibility> ..

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility-third-party-container/samples/) ([nasıl indirileceği](xref:index#how-to-download-a-sample))

Örnek uygulama, bir uygulama tarafından ara yazılım etkinleştirmesini gösterir `IMiddlewareFactory` `SimpleInjectorMiddlewareFactory` . Örnek, [basit Injector](https://simpleinjector.org) bağımlılık ekleme (dı) kapsayıcısını kullanır.

Örneğin ara yazılım uygulamasının bir sorgu dizesi parametresi () tarafından belirtilen değeri kaydeder `key` . Ara yazılım, sorgu dizesi değerini bellek içi bir veritabanına kaydetmek için eklenen bir veritabanı bağlamını (kapsamlı bir hizmet) kullanır.

> [!NOTE]
> Örnek uygulama, yalnızca tanıtım amacıyla [basit Injector](https://github.com/simpleinjector/SimpleInjector) kullanır. Basit Injector kullanımı bir onay değildir. Basit Injector belgelerinde açıklanan ara yazılım etkinleştirme yaklaşımları ve GitHub sorunları, basit Injector 'ın sürdürülebilir tarafından önerilir. Daha fazla bilgi için bkz. [basit Injector belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html) ve [basit injecgithub deposu](https://github.com/simpleinjector/SimpleInjector).

## <a name="imiddlewarefactory"></a>Imıddliwarefactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar.

Örnek uygulamada bir örnek oluşturmak için bir ara yazılım fabrikası uygulanır `SimpleInjectorActivatedMiddleware` . Ara yazılım fabrikası, ara yazılımı çözümlemek için basit Injector kapsayıcısını kullanır:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorMiddlewareFactory.cs?name=snippet1&highlight=5-8,12)]

## <a name="imiddleware"></a>Imıddliware

<xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık düzeni için ara yazılımı tanımlar.

Bir uygulama tarafından etkinleştirilen ara yazılım `IMiddlewareFactory` (*Ara yazılım/Simpleınjectoractivatedara yazılım. cs*):

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/SimpleInjectorActivatedMiddleware.cs?name=snippet1)]

Ara yazılım (*Ara yazılım/MiddlewareExtensions. cs*) için bir uzantı oluşturulur:

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Middleware/MiddlewareExtensions.cs?name=snippet1)]

`Startup.ConfigureServices`birkaç görev gerçekleştirmeniz gerekir:

* Basit Injector kapsayıcısını ayarlayın.
* Fabrika ve ara yazılımı kaydedin.
* Uygulamanın veritabanı bağlamını basit ınjtor kapsayıcısından kullanılabilir hale getirin.

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

Ara yazılım, içindeki istek işleme ardışık düzenine kaydedilir `Startup.Configure` :

[!code-csharp[](extensibility-third-party-container/samples/2.x/SampleApp/Startup.cs?name=snippet2&highlight=12)]

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* [Ara yazılım](xref:fundamentals/middleware/index)
* [Fabrika tabanlı ara yazılım etkinleştirme](xref:fundamentals/middleware/extensibility)
* [Basit ınjecgithub deposu](https://github.com/simpleinjector/SimpleInjector)
* [Basit Injector belgeleri](https://simpleinjector.readthedocs.io/en/latest/index.html)
