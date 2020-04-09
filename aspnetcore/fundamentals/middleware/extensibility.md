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
# <a name="factory-based-middleware-activation-in-aspnet-core"></a>ASP.NET Core'da fabrika tabanlı ara yazılım etkinleştirme

::: moniker range=">= aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>[ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>uzatma yöntemleri, bir ara yazılımın kayıtlı <xref:Microsoft.AspNetCore.Http.IMiddleware>türü uygulanıp uygulanmayanolmadığını denetler. Varsa, kapsayıcıda <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kayıtlı örnek, kuralı tabanlı <xref:Microsoft.AspNetCore.Http.IMiddleware> ara yazılım etkinleştirme mantığını kullanmak yerine uygulamayı çözmek için kullanılır. Ara yazılım, uygulamanın servis kapsayıcısında [kapsamlı veya geçici](xref:fundamentals/dependency-injection#service-lifetimes) bir hizmet olarak kaydedilir.

Avantajlar:

* İstemci isteği başına etkinleştirme (kapsamlı hizmetlerin enjeksiyonu)
* Middleware güçlü yazma

<xref:Microsoft.AspNetCore.Http.IMiddleware>istemci isteği (bağlantı) başına etkinleştirilir, böylece kapsamlı hizmetler ara yazılımın oluşturucusuna enjekte edilebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar. [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler <xref:System.Threading.Tasks.Task> ve ara yazılımın yürütülmesini temsil eden bir döndürür.

Ara yazılım lar sözleşme ile etkinleştirilir:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>tarafından etkinleştirildi:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Uzantılar ara yazılımlar için oluşturulur:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nesneleri fabrikada etkinleştirilen ara yazılıma geçirmek mümkün <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>değildir:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Fabrikada etkinleştirilen ara yazılım, dahili `Startup.ConfigureServices`konteynere aşağıdaki şekilde eklenir:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Her iki ara yazılım da istek `Startup.Configure`işleme boru hattında kayıtlıdır:

[!code-csharp[](extensibility/samples/3.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=12-13)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar. Ara yazılım fabrikası uygulaması kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.

Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>/<xref:Microsoft.AspNetCore.Http.IMiddleware>[ara yazılım](xref:fundamentals/middleware/index) etkinleştirme için bir genişletilebilirlik noktasıdır.

<xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>uzatma yöntemleri, bir ara yazılımın kayıtlı <xref:Microsoft.AspNetCore.Http.IMiddleware>türü uygulanıp uygulanmayanolmadığını denetler. Varsa, kapsayıcıda <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> kayıtlı örnek, kuralı tabanlı <xref:Microsoft.AspNetCore.Http.IMiddleware> ara yazılım etkinleştirme mantığını kullanmak yerine uygulamayı çözmek için kullanılır. Ara yazılım, uygulamanın servis kapsayıcısında [kapsamlı veya geçici](xref:fundamentals/dependency-injection#service-lifetimes) bir hizmet olarak kaydedilir.

Avantajlar:

* İstemci isteği başına etkinleştirme (kapsamlı hizmetlerin enjeksiyonu)
* Middleware güçlü yazma

<xref:Microsoft.AspNetCore.Http.IMiddleware>istemci isteği (bağlantı) başına etkinleştirilir, böylece kapsamlı hizmetler ara yazılımın oluşturucusuna enjekte edilebilir.

[Örnek kodu görüntüleme veya indirme](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/middleware/extensibility/samples) ( nasıl[indirilir](xref:index#how-to-download-a-sample))

## <a name="imiddleware"></a>IMiddleware

<xref:Microsoft.AspNetCore.Http.IMiddleware>uygulamanın istek ardışık alanı için ara yazılım tanımlar. [InvokeAsync (HttpContext, RequestDelegate)](xref:Microsoft.AspNetCore.Http.IMiddleware.InvokeAsync*) yöntemi istekleri işler <xref:System.Threading.Tasks.Task> ve ara yazılımın yürütülmesini temsil eden bir döndürür.

Ara yazılım lar sözleşme ile etkinleştirilir:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/ConventionalMiddleware.cs?name=snippet1)]

Ara yazılım <xref:Microsoft.AspNetCore.Http.MiddlewareFactory>tarafından etkinleştirildi:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/FactoryActivatedMiddleware.cs?name=snippet1)]

Uzantılar ara yazılımlar için oluşturulur:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Middleware/MiddlewareExtensions.cs?name=snippet1)]

Nesneleri fabrikada etkinleştirilen ara yazılıma geçirmek mümkün <xref:Microsoft.AspNetCore.Builder.UseMiddlewareExtensions.UseMiddleware*>değildir:

```csharp
public static IApplicationBuilder UseFactoryActivatedMiddleware(
    this IApplicationBuilder builder, bool option)
{
    // Passing 'option' as an argument throws a NotSupportedException at runtime.
    return builder.UseMiddleware<FactoryActivatedMiddleware>(option);
}
```

Fabrikada etkinleştirilen ara yazılım, dahili `Startup.ConfigureServices`konteynere aşağıdaki şekilde eklenir:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet1&highlight=6)]

Her iki ara yazılım da istek `Startup.Configure`işleme boru hattında kayıtlıdır:

[!code-csharp[](extensibility/samples/2.x/MiddlewareExtensibilitySample/Startup.cs?name=snippet2&highlight=13-14)]

## <a name="imiddlewarefactory"></a>IMiddlewareFactory

<xref:Microsoft.AspNetCore.Http.IMiddlewareFactory>ara yazılım oluşturmak için yöntemler sağlar. Ara yazılım fabrikası uygulaması kapsayıcıda kapsamlı bir hizmet olarak kaydedilir.

Varsayılan <xref:Microsoft.AspNetCore.Http.IMiddlewareFactory> uygulama, <xref:Microsoft.AspNetCore.Http.MiddlewareFactory> [Microsoft.AspNetCore.Http](https://www.nuget.org/packages/Microsoft.AspNetCore.Http/) paketinde bulunur.

::: moniker-end

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/index>
* <xref:fundamentals/middleware/extensibility-third-party-container>
