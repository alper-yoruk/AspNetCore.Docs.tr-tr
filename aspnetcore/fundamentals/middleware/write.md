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
# <a name="write-custom-aspnet-core-middleware"></a>Özel ASP.NET Core middleware yazın

Yazar: [Rick Anderson](https://twitter.com/RickAndMSFT) ve [Steve Smith](https://ardalis.com/)

Middleware, istek ve yanıtları işlemek için bir uygulama ardışık hattında bir araya getirilen yazılımdır. ASP.NET Core, zengin bir yerleşik ara yazılım bileşeni kümesi sağlar, ancak bazı senaryolarda özel bir ara yazılım yazmak isteyebilirsiniz.

## <a name="middleware-class"></a>Ara yazılım sınıfı

Middleware genellikle bir sınıfta kapsüllenir ve bir uzatma yöntemi ile maruz kalmaktadır. Bir sorgu dizesinden geçerli istek için kültür ayarlar aşağıdaki ara yazılım, düşünün:

[!code-csharp[](write/snapshot/StartupCulture.cs)]

Önceki örnek kod, bir ara yazılım bileşeni oluşturmayı göstermek için kullanılır. ASP.NET Core'un yerleşik yerelleştirme desteği <xref:fundamentals/localization>için bkz.

Kültür geçerek ara test edin. Örneğin, istek `https://localhost:5001/?culture=no`.

Aşağıdaki kod ara yazılım temsilcisini bir sınıfa taşır:

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

Ara yazılım sınıfı şunları içermelidir:

* Bir tür <xref:Microsoft.AspNetCore.Http.RequestDelegate>parametresi olan bir ortak yapıcı.
* Adlandırılmış `Invoke` genel `InvokeAsync`bir yöntem veya . Bu yöntem şunları yapmalı:
  * Bir `Task`.
  * Türünün <xref:Microsoft.AspNetCore.Http.HttpContext>ilk parametresini kabul edin.
  
Yapıcı için ek parametreler `Invoke` / `InvokeAsync` ve [bağımlılık enjeksiyonu (DI)](xref:fundamentals/dependency-injection)tarafından doldurulur.

## <a name="middleware-dependencies"></a>Ara yazılım bağımlılıkları

Ara yazılım, yapıcısındaki bağımlılıklarını ortaya çıkararak [Açık Bağımlılıklar İlkesi'ni](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) izlemelidir. Middleware *uygulama ömrü*başına bir kez inşa edilmiştir. Bir istek içinde ara yazılımlarla hizmetleri paylaşmanız [gerekiyorsa, isteğ başına](#per-request-middleware-dependencies) ara yazılım bağımlılıkları bölümüne bakın.

Ara yazılım bileşenleri bağımlılıklarını [bağımlılık enjeksiyonundan (DI)](xref:fundamentals/dependency-injection) oluşturucu parametreleri aracılığıyla çözebilir. [UseMiddleware&lt;&gt; T](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) ayrıca ek parametreleri doğrudan kabul edebilir.

## <a name="per-request-middleware-dependencies"></a>İstek başına ara yazılım bağımlılıkları

Ara yazılım, istek başına değil, uygulama başlangıç noktasında oluşturulduğundan, ara yazılım üreticileri tarafından kullanılan *kapsamlı* yaşam süresi hizmetleri her istek sırasında diğer bağımlılık enjekte edilen türler ile paylaşılmaz. Ara yazılımınız ve diğer türler arasında *kapsamlı* bir hizmet paylaşmanız `Invoke` gerekiyorsa, bu hizmetleri yöntemin imzasına ekleyin. Yöntem, `Invoke` DI tarafından doldurulan ek parametreleri kabul edebilir:

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

## <a name="middleware-extension-method"></a>Ara yazılım uzatma yöntemi

Aşağıdaki uzatma yöntemi aracılığı ile <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>middleware ortaya çıkarır:

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

Aşağıdaki kod middleware `Startup.Configure`çağırır:

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a>Ek kaynaklar

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
